---
layout: single
title:  "PaintableTexture With Custom Brush Texture"
categories: shader
---
<img src="https://github.com/Mati-as/mati-as.github.io/assets/120005151/c7ee7ba9-856f-4061-9263-c12d21f7187f" width="500" height="500">
<img src="https://github.com/Mati-as/mati-as.github.io/assets/120005151/2553ad18-d8f2-46c0-8c20-a89f20db3da4" width="500" height="500">
<img src="https://github.com/Mati-as/mati-as.github.io/assets/120005151/809002d9-9513-4644-bdd6-af493b59c6cd" width="500" height="500">
<img src="https://github.com/Mati-as/mati-as.github.io/assets/120005151/602bf25d-61b1-47d8-a58f-dc715a66754e" width="500" height="500">


```
Shader "Custom/PaintShader"
{
  Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _MouseUV ("Mouse UV", Vector) = (-1,-1,0,0)
        _BrushSize ("Brush Size", Float) = 0.1
        _BrushStrength ("Brush Strength", Float) = 0
        _Color ("Color", Color) = (1,1,1,1)
        _BrushColor ("Brush Color", Color) = (0,0,0,0)
        _BrushTex ("Brush Texture", 2D) = "white" {}
    }
    SubShader
    {
       Tags{"RenderPipeline"= "UniversalPipeline"  "RenderType"= "Transparent" "RenderQueue" = "Transparent"}
        LOD 100
        Pass
        {
            Blend SrcAlpha OneMinusSrcAlpha
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
            float4 _MouseUV;
            float _BrushStrength;
            sampler2D _MainTex;
            sampler2D _BrushTex;
            float _BrushSize;
            float4 _Color;
            float4 _BrushColor;

            v2f vert (appdata v)
            {
                v2f o;
                o.uv = v.uv;
                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
            fixed4 col = tex2D(_MainTex, i.uv);
            float2 brushUV = (i.uv - _MouseUV.xy) / _BrushSize + 0.5; // Normalization
            fixed4 brushCol = tex2D(_BrushTex, brushUV)

            
            float brushAlpha = brushCol.a;
            if (distance(i.uv, _MouseUV.xy) < _BrushSize)
            {
                //this(co.rgb) can be used when color needs changing.
                //col.rgb = lerp(col.rgb, brushCol.rgb, brushAlpha * _BrushStrength);
                col.a = lerp(col.a, brushCol.a, brushAlpha * _BrushStrength);
            }

            return col;
                       
                    }
                    ENDCG
                }
    }
   
}
               
```

## Debugged Tiling Error (12/20/23)
```
Shader "Custom/PaintShader"
{
  Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _MouseUV ("Mouse UV", Vector) = (-1,-1,0,0)
        _BrushSize ("Brush Size", Float) = 0.1
        _BrushStrength ("Brush Strength", Float) = 0
        _Color ("Color", Color) = (1,1,1,1)
        _BrushColor ("Brush Color", Color) = (0,0,0,0)
        _BrushTex ("Brush Texture", 2D) = "white" {}
        _BrushTexTilingOffset ("Brush Tex Tiling and Offset", Vector) = (1,1,0,0)
      
    }
    SubShader
    {
       Tags{"RenderPipeline"= "UniversalPipeline"  "RenderType"= "Transparent" "RenderQueue" = "Transparent"}
        
        LOD 100
        Pass
        {
            Blend SrcAlpha OneMinusSrcAlpha
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

              
    
            float4 _MouseUV;
            float _BrushStrength;
            sampler2D _MainTex;
            sampler2D _BrushTex;
            float _BrushSize;
            float4 _Color;
            float4 _BrushColor;
            float4 _BrushTexTilingOffset;
            
            
           
            v2f vert (appdata v)
            {
                v2f o;
                o.uv = v.uv;
                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {

               fixed4 col = tex2D(_MainTex, i.uv);
                float2 brushUV = (i.uv - _MouseUV.xy) / _BrushSize + 0.5;
                          
                brushUV = clamp(brushUV, 0.0, 1.0);

           
                if (brushUV.x == 0.0 || brushUV.x == 1.0 || brushUV.y == 0.0 || brushUV.y == 1.0)
                {
                    return col;
                }
                fixed4 brushCol = tex2D(_BrushTex, brushUV);

             
                float brushAlpha = brushCol.a;

             
                if (distance(i.uv, _MouseUV.xy) < _BrushSize)
                {
                    col.a = lerp(col.a, 0, brushAlpha * _BrushStrength);
                }

                return col;
            }
            ENDCG
        }
    }
}
```

