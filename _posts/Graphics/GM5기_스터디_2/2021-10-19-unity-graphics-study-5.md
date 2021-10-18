---
title: "[Unity Graphics Study] 5. Shader(2) - Outline, Rim light"
date: 2021-10-19 03:00:00 +0900
categories: [Graphics, GM5기_스터디_2]
tags: [cg, culling, fresnel, shader, shaderlab, study, unity]
---

> 스터디에서 배운 내용이지만, 아래 글은 개인적으로 복습·정리한 것이므로 틀린 부분이 있을 수 있습니다.  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

7주차에는 3D 오브젝트에 외곽선 및 림 라이트, 디졸브 셰이더를,  2D 오브젝트에 왜곡 효과 셰이더를 만들어 적용해보았다.  
사실 실습에서 사용한 Amplify shader editor에는 Outline, Fresnel과 같은 노드가 있어 쉽게 적용할 수 있었다. 그래서 빠르게 진행했던 부분인데, 스크립트로는 직접 구현해주어야 하기 때문에 이번 7주차 내용은 스터디보단 아무래도 전에 공부했던 셰이더 책(유니티 쉐이더 스타트업) 복습에 가까울듯하다.  

---
<br>

## **실습 전 설정**
간단한 수준의 셰이더를 만들 것이므로 실습 전 코드를 간단히 정리했다.  
1. Create -> Shader -> Standard Surface Shader 생성
2. MainTex를 제외한 프로퍼티 및 변수 모두 제거
3. LOD 옵션 제거
4. 전처리(스니핏) 수정: 셰이더 모델 설정 제거, Lambert 라이팅 사용
5. GPU 인스턴싱 사용 안함
6. 서피스 셰이더 수정: 구형 라이팅 구조체 사용(SurfaceOutput), Metallic과 Smoothness 세팅 제거

### **코드**

```c
Shader "Custom/Week7/Shader_01"
{
    Properties
    {
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }

        CGPROGRAM
        #pragma surface surf Lambert

        sampler2D _MainTex;

        struct Input
        {
            float2 uv_MainTex;
        };

        void surf (Input IN, inout SurfaceOutput o)
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
            o.Alpha = c.a;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```

### **결과**
3D 오브젝트([Space Robot Kyle](https://assetstore.unity.com/packages/3d/characters/robots/space-robot-kyle-4696?locale=ko-KR))에 적용해보았다.

![001](/assets/img/post_images/20211019001.png)

<br>

## **실습 1: 외곽선 셰이더**
외곽선은 만화같은 그래픽 스타일이나 오브젝트 선택 시 하이라이트 등에 흔히 사용되는 효과이다.  

### 원리
외곽선 느낌을 만드는 방법은 여러가지가 있는데, 그 중 2-Pass 방식의 원리는 이렇다.  
2개의 패스를 사용하는 방식, 즉 오브젝트를 두 번 그린다는 뜻이다.  
1. 외곽선 그리기: 오브젝트를 노멀 방향으로 확장 -> 면 뒤집기 -> 색 입히기
2. 기존 오브젝트 그리기

노멀 방향으로 확장하는 것은, 오브젝트의 피벗을 기준으로 확장시키면 실루엣이 어긋날 수 있기 때문이고  
면을 뒤집는 이유는 외곽선이 기존 오브젝트에서 확장된 상태이므로 기존 오브젝트를 가려버리기 때문이다.  
(처음에 '면을 뒤집으면 기본 오브젝트가 보여진다'는 개념을 이해하기가 약간 어려웠는데, 렌더링 순서에 대한 이해가 부족했고 메쉬를 뒤집었을 때의 모습이 생소해서 그랬던 것 같다.  
3D 모델을 속이 빈 귤 껍질로 생각하고 껍질을 뒤집은 모습을 생각하며, 컴퓨터가 귤 껍질 안쪽을 먼저 그리고 바깥쪽을 그리기 때문에 기본 오브젝트가 보이게 된 거라고 생각했다.)  

### **코드**
(노드로는 `Outline` 노드를 사용해 색상,두께 프로퍼티만 추가해주었기 때문에 내용 생략)  
코드로는 이렇게 진행했다.  

2-Pass 방식의 외곽선을 그리기 위해서 먼저 `CGPROGRAM ~ ENDCG` 구역을 복붙해줌으로써 하나의 패스를 추가해주었다. Pass 1은 외곽선을, Pass 2는 기존 오브젝트를 그대로 그리는 부분이다.  

1. **외곽선 렌더링(Pass 1)**  
이 패스에서는 버텍스를 건드려야 하므로 버텍스 셰이더 함수를 추가하고, 그림자와 환경광이 필요없으므로 그에 맞게 전처리문을 수정해주었다.  
그리고 아래와 같이 진행했다.  
**① 버텍스 확장(프로퍼티 적용):** 버텍스 셰이더에 각 버텍스를 노멀 방향으로 확장하는 코드 추가. 이때 프로퍼티로 확장 수치를 조정가능하도록 함  
**② 면 뒤집기:** 1번째 패스 시작 전 'cull front'를 써주어 뒷면이 아닌 앞면이 컬링되도록 함  
**③ 색 입히기(프로퍼티 적용):** 라이팅 계산이 필요없는 패스이므로 커스텀 라이트(Nolight)를 이용해 가장 가벼운 라이팅 구조를 만들어줌. 이때 프로퍼티로 색상을 조정가능하도록 함  

2. **기존 오브젝트 렌더링(Pass 2)**  
**면 되돌리기:** 여기서는 면이 원래대로 렌더링되어야 하므로, 2번째 패스 시작 전 'cull back'을 써줌

```c
Shader "Custom/Week7/Shader_01"
{
    Properties
    {
        _MainTex("Albedo (RGB)", 2D) = "white" {}
        _OutlineColor("Outline Color", Color) = (0,0,0,1)
        _OutlineWidth("Outline Width", Range(0,0.01)) = 0.005
    }
    SubShader
    {
        Tags { "RenderType" = "Opaque" }
        LOD 200

        cull front
        // 1st Pass
        CGPROGRAM
        #pragma surface surf Nolight vertex:vert noshadow noambient

        fixed4 _OutlineColor;
        float _OutlineWidth;

        void vert(inout appdata_full v) {
            v.vertex.xyz = v.vertex.xyz + v.normal.xyz * _OutlineWidth;
        }

        struct Input
        {
            float4 color:COLOR;
        };

        void surf (Input IN, inout SurfaceOutput o)
        {
        }

        float4 LightingNolight(SurfaceOutput s, float3 lightDir, float atten)
        {
            return _OutlineColor;
        }
        ENDCG

        cull back
        // 2nd Pass
        CGPROGRAM
        #pragma surface surf Lambert

        sampler2D _MainTex;

        struct Input
        {
            float2 uv_MainTex;
        };

        void surf(Input IN, inout SurfaceOutput o)
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```

### **결과**

- **Outline Color(RGBA) 값:** 0,255,0,255
- **Outline width 값:** 0.003
- **출력**  
![002](/assets/img/post_images/20211019002.png)

<br>

## **실습 2: 림 라이트 셰이더**
림 라이트는 피사체의 뒤쪽에서 빛을 비추었을 때 나타나는 후광 효과이다. 물체의 외곽에 빛으로 된 선이 생긴 형태인데, 게임에서는 이를 이용하여 다양한 효과를 낼 수 있다. 오브젝트를 배경으로부터 분리하거나 강조할 때, 버프 등의 특정 상태를 표현하고자 할 때, 또는 외곽선을 만드는 방법 중 하나이기도 하다. 오브젝트를 그리지 않고 림 라이트만을 그려 홀로그램 느낌을 낼 수도 있다.  

### 원리
림 라이트는 '프레넬 현상'과 관련이 있다. 프레넬 현상과 그 공식에 대해서는 따로 정리하는게 좋을듯하여 별도로 포스팅할 예정이지만(아직 글 정리를 다 못함 ㅠㅠ),  
아무튼 아주 간단히 말하자면 보는 각도에 따라 반사율이 달라지는 현상을 말하는 것이다.  
아래 구현한 것은 실제 빛의 방향과 상관 없으며 훨씬 간략한 공식을 사용한 림 라이트로, 다음과 같이 만들어졌다.  
1. 노멀 벡터와 뷰 벡터 내적 연산: 이렇게 하면 마치 카메라가 빛을 내뿜는 것처럼 되어서 오브젝트를 어느 쪽에서 바라보아도 무조건 카메라를 마주한 면이 밝게 보인다.
2. 밝기 반전
3. 밝기 변화를 더 극단적으로: 림 라이트의 느낌을 살린다.

1번은 단순히 노멀 벡터와 조명 벡터를 내적 연산하는 램버트 라이팅 공식에서 조명 벡터를 뷰 벡터로 바꾼 것이다.  

### **코드**
(노드로는 `Fresnel` 노드를 사용해 프로퍼티만 추가해주었기 때문에 설명 생략)  
코드로는 이렇게 진행했다.  

일단 내적 연산에 노멀 벡터를 사용하기 때문에, 노멀맵을 받아 노멀값을 연산해주어야 하므로 프로퍼티를 추가해주었다.  
1. **노멀 벡터와 뷰 벡터 내적 연산**  
① Input 구조체에서 뷰 벡터 값을 받아 노멀 벡터와 내적 연산한 값을 저장(float rim)  
② saturate()를 사용하여 0~1 범위를 벗어나지 않도록 함
2. **밝기 반전:** 1에서 구한 값을 1에서 빼줌(1-rim)
3. **밝기 변화 조정(프로퍼티 적용):** 2에서 구한 값에 pow()를 사용하여 제곱 연산해줌으로써 림 라이트의 느낌을 살림. 이때 지수와 색상을 프로퍼티로 조정가능하도록 함
4. **위 값을 Emission에 적용**

```c
Shader "Custom/Week7/Shader_02"
{
    Properties
    {
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        _BumpMap("Normal Map", 2D) = "bump" {}
        _RimColor("Rim Color", Color) = (1,1,1,1)
        _RimPower("Rim Power", Range(0,5)) = 2
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }

        CGPROGRAM
        #pragma surface surf Lambert

        sampler2D _MainTex;
        sampler2D _BumpMap;
        fixed4 _RimColor;
        float _RimPower;

        struct Input
        {
            float2 uv_MainTex;
            float2 uv_BumpMap;
            float3 viewDir;
        };

        void surf (Input IN, inout SurfaceOutput o)
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
            o.Normal = UnpackNormal(tex2D(_BumpMap, IN.uv_BumpMap));
            float rim = saturate(dot(o.Normal, IN.viewDir));
            o.Emission = pow(1-rim, _RimPower) * _RimColor;
            o.Alpha = c.a;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```

### **결과**

- **Rim Color(RGBA) 값:** 255,0,0,255
- **Rim Power 값:** 1.5
- **출력**  
![003](/assets/img/post_images/20211019003.png)

<br>

---

이번 셰이더도 사실 그렇게 복잡하지는 않은 것들이었지만 이번에는 잘 모르고 있었던 원리를 짚고 넘어가려다 보니 추가로 공부하느라 꽤나 시간이 걸린 것 같다. 그래도 원리조차 제대로 알지도 못하고 넘어갈뻔한 걸 어느정도 알게 되었으니 의미있는 시간 투자였다고 생각한다.  
하지만 스터디 끝난지 열흘이 다 돼 가는데 아직 쓸게 3개나 남아서 속도를 좀 내야될 것 같긴 하다...🙄 이왕 시작했으니 끝까지 써보자..!!  
