---
layout: post
title: "Window Shaders"
date: 2019-02-15 12:00:00 +0100
tags: Unity
---
Some games use shaders for windows to make it look like there is a three-dimensional interior behind the window without needing any additional geometry. This can have a much lower performance impact than modelled interiors, which is good for situations like city scenes where there are hundreds of windows.

I created some window shaders in Unity using several different techniques to create the appearance of depth.

### Shader 1: Cubemap
One basic approach is to sample a cubemap using the view direction. This would only be effective for very large interiors.

![Cubemap Interior]({{ "/assets/window shaders/WindowCubemap.gif" | absolute_url }})

### Shader 2: Back wall
A more convincing effect can be created by sampling a 2D texture as though it were positioned some distance behind the window's surface. This can be very efficient, because almost all of the calculations can be done in the vertex shader. I've included the code for this shader at the bottom of the page.

![Plane Interior]({{ "/assets/window shaders/WindowPlane.gif" | absolute_url }})

### Shader 3: Walls, ceiling and floor
I also tried recreating the window shader used in Forza Horizon 4, which is discussed in [this Gamasutra article](https://www.gamasutra.com/view/news/332409/Game_Tech_Deep_Dive_A_window_into_Playground_Games_latest_shader_development.php). This shader takes a square 2D texture and projects it so it appears to be a cuboid-shaped interior.

The gif below shows my shader in action. I've also included the 2D texture, which I made using Blender. I've used a bay window to demonstrate that the effect works regardless of the shape of the window's mesh.

![Forza Interior]({{ "/assets/window shaders/WindowForza.gif" | absolute_url }})

![Interior Texture]({{ "/assets/window shaders/Room Thirds.png" | absolute_url }})

### Shader 2 code
This is the code for shader 2, described above. The alpha channel of the albedo texture (_MainTex) determines where the interior should be visible. The _IndoorOffset 4D vector represents two 2D vectors, one for the position of the interior texture and one for its scale.

    Shader "Custom/Window Shader"
    {
        Properties
        {
            _MainTex("Albedo (RGB)", 2D) = "white" {}
            _SpecularTex("Specular", 2D) = "black" {}
            _NormalTex("Normal", 2D) = "bump" {}
            _IndoorTex("Indoor texture", 2D) = "white" {}
            _Depth("Depth", Range(0,5)) = 1
            _IndoorOffset("Indoor position and scale", Vector) = (0,0,1,1)
            _IndoorTint("Indoor tint", Color) = (1,1,1,1)
            _BackgroundColor("Background Color", Color) = (0,0,0,1)
        }
        SubShader
        {
            Tags { "RenderType"="Opaque" }
            LOD 200

            CGPROGRAM
            // Physically based Standard lighting model, and enable shadows on all light types
            #pragma surface surf Standard fullforwardshadows vertex:vert

            // Use shader model 3.0 target, to get nicer looking lighting
            #pragma target 3.0

            struct Input
            {
                float2 uv_MainTex;
                float2 uv_SpecularTex;
                float2 uv_NormalTex;
                float2 indoorUV;
            };

            sampler2D _MainTex;
            sampler2D _SpecularTex;
            sampler2D _NormalTex;
            sampler2D _IndoorTex;
            half _Depth;
            half4 _IndoorOffset;
            fixed4 _IndoorTint;
            fixed4 _BackgroundColor;

            // Add instancing support for this shader. You need to check 'Enable Instancing' on materials that use the shader.
            // See https://docs.unity3d.com/Manual/GPUInstancing.html for more information about instancing.
            // #pragma instancing_options assumeuniformscaling
            UNITY_INSTANCING_BUFFER_START(Props)
            // put more per-instance properties here
            UNITY_INSTANCING_BUFFER_END(Props)

            void vert (inout appdata_full v, out Input o)
            {
                UNITY_INITIALIZE_OUTPUT(Input, o);

                // Find the point on the indoor wall that is being viewed
                half3 viewVectorLocal = ObjSpaceViewDir(v.vertex);
                half2 indoorPoint = v.vertex + viewVectorLocal * (_Depth - v.vertex.z) / viewVectorLocal.z;

                // Transform it using "Indoor position and scale" values
                indoorPoint -= half2 (_IndoorOffset.x, _IndoorOffset.y);
                indoorPoint /= half2 (_IndoorOffset.z, _IndoorOffset.w);
                indoorPoint += half2 (0.5, 0.5);

                o.indoorUV = indoorPoint;
            }

            void surf (Input IN, inout SurfaceOutputStandard o)
            {
                fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
                o.Albedo = c.rgb * c.a;
                o.Smoothness = tex2D(_SpecularTex, IN.uv_SpecularTex).r;
                o.Normal = UnpackNormal(tex2D(_NormalTex, IN.uv_NormalTex));

                if (IN.indoorUV.x > 0 &&
                    IN.indoorUV.x < 1 &&
                    IN.indoorUV.y > 0 &&
                    IN.indoorUV.y < 1)
                {
                    o.Emission = tex2D(_IndoorTex, IN.indoorUV) * _IndoorTint * (1 - c.a);
                }
                else
                {
                    o.Emission = _BackgroundColor * (1 - c.a);
                }
            }
            ENDCG
        }
        FallBack "Diffuse"
    }