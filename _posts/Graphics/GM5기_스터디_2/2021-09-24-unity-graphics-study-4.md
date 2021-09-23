---
title: "[Unity Graphics Study] 4. Shader(1)"
date: 2021-09-24 02:10:00 +0900
categories: [Graphics, GM5기_스터디_2]
tags: [cg, shader, shaderlab, study, unity]
---

> 스터디에서 배운 내용이지만, 아래 글은 개인적으로 복습·정리한 것이므로 틀린 부분이 있을 수 있습니다.  
틀린 부분이 있다면 지적해주시면 감사하겠습니다🙏  

<br>

6주차부터는 대망의(?) 셰이더 파트다. 이 파트는 몇주 간 진행될 내용이지만 내용이 길어 주차별로 나누어 적으려 한다.  
그런데 문제는... 스터디에서 사용하는 툴이 유료 에셋([Amplify shader editor](https://assetstore.unity.com/packages/tools/visual-scripting/amplify-shader-editor-68570?locale=ko-KR))이다. 유니티에도 Shader Graph라는 자체 비주얼 노드 에디터가 있기는 하나 강의하시는 분께서 이게 더 편리하고 좋아서 이 패키지를 사용하신다고 한다.(언리얼의 블루프린트와 비슷한 것 같다.)  
실습을 어떻게 따라할까 고민한 끝에 스터디때는 필기만 좀 해두고 이후에 동일한 셰이더를 코드로 한번 구현해보기로 했다. 아무래도 시간이 꽤나 걸리겠지만, 예전에 책을 보며 셰이더 만드는 실습을 했던적이 있으니 복습도 할겸... 미래의 내가 무슨말을 하게 될지 모르겠지만 일단 도전해본다=_=;;ㅋㅋ  
암튼 그래서 노드 설명같은 것들은 스크립트 코드로 치환해 정리해볼 예정이다.  

---
<br>

## **시작하기 전에: (매우 간략한)렌더링 파이프라인**
어떤 3D 그래픽 데이터가 2D 화면에 출력(렌더링)되기 위해서는 여러 단계(파이프라인)를 거치게 되는데, 이를 아주 간략하게 정리하면 다음과 같다.  

1. **Vertex shader** stage: 각 정점의 정보를 받아 화면상의 공간으로 변환하고 음영을 계산
2. **Rasterizer** stage: 정점들을 서로 연결하고 화면에 띄울 준비를 함(화면상의 어느 픽셀에 찍힐 것인지를 결정)
3. **Pixel shader** stage: 픽셀 당 화면에 출력할 최종 컬러를 계산(폴리곤을 픽셀로 채움)

물론 실제 렌더링 프로세스는 이보다 더욱 복잡하지만 굳이 여기서 설명을 늘어놓고 싶진 않으므로 생략한다...따로 글을 쓰는게 나을듯!  
어쨌든 이 단계 중에서 커스텀 가능한, 즉 프로그래머블한 단계는 1번과 3번이고, 스터디에서는 3번 픽셀 셰이더에 대해서만 다룬다.  

<br>

## **실습 전 설정**
셰이더의 `General` property를 아래와 같이 설정해주었다.  

- **Light Model**: Unlit (이번 실습에서는 라이팅 연산을 사용하지 않는다)
- **Cull Mode**: Off (모델링 뒷면까지 렌더링)
- **Cast Shadows, Receive Shadows**: 체크 해제 (그림자 사용하지 않음)

코드로는 이렇게 진행했다.  

1. Create -> Shader -> Unlit Shader 생성
2. 기본 코드 변경: Fog, Texture 관련 코드 제거 ([참고](https://www.slideshare.net/crjl5/1-shader-199362726))
4. 코드 추가: Render Queue, Cull Mode 세팅
5. frag()의 return 값을 0으로 초기화

(Cast Shadows, Receive Shadows는 Unlit이라 기본적으로 적용되진 않는 듯한데... 혹시 따로 설정하는 코드가 있다면 추가해야겠다.)  

### **코드**

```c
Shader "Unlit/Custom/Shader_01"
{
    Properties
    {
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" "Queue" = "Geometry" }
        LOD 100

        Pass
        {
            Cull Off

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            // Vertex Shader
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            // Fragment Shader
            fixed4 frag(v2f i) : SV_Target
            {
                return 0;
            }
            ENDCG
        }
    }
}
```

### **결과**
Quad 오브젝트에 적용해보았다. 아직 아무것도 적용되지 않은 상태로, 다음과 같다.  

![001](/assets/img/post_images/20210924001.png)

*※ 실습 내용을 정리하며 Amplify Shader Editor에서 쓰인 노드를 언급하기는 할테지만, 나는 사용하지 않았으므로 딱히 자세히 쓰진 않았다. (잘 설명되어 있는 블로그 링크가 있어 첨부함: [앰플리파이 쉐이더 입문자를 위한 60가지 노드 모음](https://rito15.github.io/posts/amplify-shader-60-nodes-for-beginners/))*  

<br>

## **실습 1: 두 색상을 곱하여 출력하는 셰이더**
먼저 색상을 숫자로 인식하고 `Float`, `Vector2~4` 및 `Color` 노드를 사용하여 Emission에 연결함으로써 셰이더에 색상을 적용해보았다.  

- **Float**: 실수값
- **Vector2**: xy(RG) (B 채널이 없으므로 흰색을 표현할 수 없음)
- **Vector3**: xyz(RGB)
- **Vector4**: xyzw(RGBA)
- **Color**: RGBA (Vector4와 동일하게 사용될 수 있음)

그다음 이를 상수가 아닌 변수로 사용하기 위해 에디터에서 일일이 변경하지 않고 인스펙터창에 띄워 조절 가능하도록 노드 속성을 'Constant'에서 'Property'로 변경해주었다.  

마지막으로 `Add`, `Multiply`와 같은 연산 노드에 대해서도 알아보았다.  

코드로는 이렇게 진행했다.  

1. 2개의 컬러 프로퍼티 _Color1, _Color2 추가
2. 프로퍼티와 동일한 이름의 전역변수 선언
3. 두 변수를 곱한 값을 return (Multiply)

### **코드**
```c
Shader "Unlit/Custom/Shader_01"
{
    Properties
    {
        _Color1("Color1", Color) = (1,1,1,1)
        _Color2("Color2", Color) = (1,1,1,1)
    }
        SubShader
    {
        Tags { "RenderType" = "Opaque" "Queue" = "Geometry" }
        LOD 100

        Pass
        {
            Cull Off

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            fixed4 _Color1;
            fixed4 _Color2;

            // Vertex Shader
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            // Fragment Shader
            fixed4 frag(v2f i) : SV_Target
            {
                // float4 col = float4(1,0,0,1);    // RED
                // return col;  // constant

                // return _Color1 + _Color2;  // Add 2 properties
                return _Color1 * _Color2; // Multiply 2 properties
            }
            ENDCG
        }
    }
}
```

### **결과**

- **컬러 값**  
![002](/assets/img/post_images/20210924002.png)
- **출력**  
![003](/assets/img/post_images/20210924003.png)

<br>

## **실습 2: 텍스처를 받아 왼쪽으로 흘러가게 하는 셰이더**
먼저 `Texture Sample` 노드를 추가하고 'MainTex'라고 이름붙인 후, 해당 노드의 RGBA 값을 Emission에 연결해줌으로써 텍스처를 받을 수 있게 했다.  

그다음 이걸 움직이게 할 건데 두 가지 방법이 있다. 각각의 노드 세팅을 이미지로 정리해보았다.  

**첫번째 방법**  

![004](/assets/img/post_images/20210924004.png)

**두번째 방법**  

![005](/assets/img/post_images/20210924005.png)

- **Texture Sample**: 참조하는 텍스처의 색을 출력
- **Texture Coordinates**: 샘플링할 텍스처 이미지의 영역을 지정. UV 타일링 및 좌표 이동 설정이 가능(Vector2 형식)
- **Time**: 시간에 따라 값이 증가함(Scale로 변화량 조정 가능)
- **Panner**: UV를 원하는 방향, 속도로 이동시킬 때 사용. Time 노드를 따로 연결하지 않아도 Time이 적용된다.

그런데 시간값을 적용했을 때 왜 하필 왼쪽으로/아래로 흐를까?  
일단 UV좌표값을 증가시키면 그 값에 해당하는 텍스처의 색을 가져온다고 생각하면 될 것 같다. 예를들어 UV좌표의 x값을 1만큼 증가시켜서 (0,0)이 (1,0)이 되었다면 텍스처의 (1,0)에 해당하는 색상으로 바뀌게 되는 것이다. 개인적으로 이해한 방식이라 이렇게 설명하는 게 옳은지는 모르겠다.ㅠ_ㅠ  
그리고 유니티 엔진의 UV 좌표계는 OpenGL 방식(오른손 좌표계)을 따른다. 즉 원점이 왼쪽 아래이고, 오른쪽 위로 향할수록 값이 증가한다.  
그래서 UV좌표값을 1씩 증가시키면 왼쪽 아래로 텍스처를 민 것 같은 결과가 나오게 되는 것이다.  
(\*여기서 조금 헷갈린게 있는데, 위에서 언급한건 UV 좌표계이고 유니티의 3D 좌표계는 DirectX와 같다(왼손 좌표계). 자세한 내용은 [이 블로그 글](https://m.blog.naver.com/destiny9720/221411002215) 참고.)  

코드로는 이렇게 진행했다.  

1. 실습 전에 지웠던 기본 코드에서 Texture 관련 코드 다시 복원
(1개의 텍스처 프로퍼티 및 전역변수 추가하고 연결)
2. UV 좌표에서 i.uv.x에 _Time.x를 더해주어 시간에 따라 x값만 변화하도록 적용
3. 속도가 너무 느려서 속도 조정을 위해 Float 프로퍼티 및 전역변수를 추가하고 _Time.x에 곱해줌

(\*tex2D의 매개변수: 텍스처 샘플러, UV 좌표)  

### **코드**
```c
Shader "Unlit/Custom/Shader_01"
{
    Properties
    {
        _MainTex("Texture", 2D) = "white" {}
        _Speed("Speed", Float) = 0
    }
        SubShader
    {
        Tags { "RenderType" = "Opaque" "Queue" = "Geometry" }
        LOD 100

        Pass
        {
            Cull Off

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;
            float _Speed;

            // Vertex Shader
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            // Fragment Shader
            fixed4 frag(v2f i) : SV_Target
            {
                fixed4 col = tex2D(_MainTex, float2(i.uv.x + (_Time.x*_Speed), i.uv.y));
                return col;
            }
            ENDCG
        }
    }
}
```

### **결과**

- **Speed 값**: 20
- **출력**  
![006](/assets/img/post_images/20210924006.gif)

<br>

---

이번 실습은 기초적인 내용이라 그런지 생각보다는 코드 작성에 시간이 걸리지는 않았다. 과연 앞으로 배우게 될 여러 셰이더들도 무난히 할 수 있을지...  
그나저나 코드를 쓰다보니 전에 책 보며 셰이더 코드 짤때 어디 블로그같은 데다가 공부한 흔적을 남겨뒀더라면 좋았을텐데 하는 아쉬운 마음이 좀 들었다. 당시에는 내가 글 쓰는데 시간이 너무 걸리는 탓에 시간이 아까워서 그냥 혼자 실습하고 말았는데 역시 공부했으면 뭐라도 남겨놓는게 좋지 않나 싶다. OTL  
