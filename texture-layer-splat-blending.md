

### TEXTURE LAYER SPLAT BLENDING 

```
Shader "Tianyu Shaders/Scene/SplatBlending/Ground2LayerSplatBlend1NormalMapSpecular" 
{
    Properties
    {
		_SplatMap01("Splat Map 01", 2D) = "black" {}
		[Header(UV1 Diffuse)]
		_MainColor("Main Color", Color) = (1,1,1,1)
        _SpecColor("Specular Color", Color) = (0.5, 0.5, 0.5, 1)
       _Albedo01("Base 0(RGB)(MAX UV1)", 2D) = "white" {}
		[NoScaleOffset][Normal]_BumpMap("Normalmap", 2D) = "bump" {}
        
        [Header(UV1 PARAMETER)]
		[NoScaleOffset][Gamma]_ParamTex("Specular(R),Glossness(G),Occlusion(B)", 2D) = "white" {}
        
        [Header(UV2 Diffuse)]
		_Albedo02("Base 2(RGB)", 2D) = "white" {}
		_ClipTex("Detail Blend(R)", 2D) = "white" {}
       
        [Header(Blend Detail)]
		_BlendScale1("Depth Scale", Range(0 , 15)) = 1
		_BlendNormal("Depth Normal", Range(0 , 0.858)) = 0.858

    }

    CGINCLUDE
#include "../../PanguShaderCommon.cginc"
	
	sampler2D _SplatMap01;
	sampler2D _Albedo01, _Albedo02, _Albedo03, _ClipTex, _ParamTex, _BumpMap;
	float4 _SplatMap01_ST, _Albedo01_ST, _Albedo02_ST, _ClipTex_ST, _ParamTex_ST , _BumpMap_ST;
	fixed3 _TintColor01, _TintColor02;
	fixed  _BlendScale1 , _BlendNormal;
	
    
    struct Input {
		float2 myUV;
        fixed4	color; //Vertex color uv
		INTERNAL_DATA
    };

	void vert(inout appdata_full v, out Input o) {

       UNITY_INITIALIZE_OUTPUT(Input, o);
//       o.color = v.color; //input to vertex blending value.
	   o.myUV = v.texcoord;

    }

    void surf(Input IN, inout SurfaceOutput o) 
    {
		fixed3 paramtex = tex2D(_ParamTex, TRANSFORM_TEX(IN.myUV, _ParamTex)).rgb;
		fixed4 splatMap01 = tex2D(_SplatMap01, TRANSFORM_TEX(IN.myUV, _SplatMap01)).rgba;
		fixed3 albedo01 = tex2D(_Albedo01, TRANSFORM_TEX(IN.myUV, _Albedo01)).rgb;
		fixed3 albedo02 = tex2D(_Albedo02, TRANSFORM_TEX(IN.myUV, _Albedo02)).rgb;
		fixed3 cliptexture = tex2D(_ClipTex, TRANSFORM_TEX(IN.myUV, _ClipTex)).rgb;
		
		
		fixed factorRed = splatMap01.r;
		fixed factorGreen = splatMap01.g; // Get from vertex color input. (G) 
		
		fixed combinedMask = clamp(pow(factorGreen + (cliptexture * _BlendScale1), 1.25), 0, 1);
		
		fixed3 texCom = flerp(albedo01, albedo02, combinedMask);
		paramtex = flerp(paramtex, fixed4(0, 0, 1, 0), combinedMask);
		
		o.Albedo = texCom * paramtex.b;
		
		o.Gloss = paramtex.g;
        
		o.Specular = paramtex.r;
		
		o.Normal = UnpackNormal(tex2D(_BumpMap, TRANSFORM_TEX(IN.myUV, _BumpMap)));
		o.Normal = flerp(o.Normal, fixed3(0, 0, 1), combinedMask * (1 - _BlendNormal));
    }
    ENDCG

        SubShader
		{
		Tags{ "RenderType" = "Opaque" "Queue" = "Geometry-100" "PerformanceChecks" = "False" }
        LOD 400
        CGPROGRAM
#pragma only_renderers d3d11 glcore gles gles3 metal
#pragma fragmentoption ARB_precision_hint_fastest
#pragma surface surf BlinnPhong vertex:vert keepalpha novertexlights nodynlightmap nometa noforwardadd 
#pragma target 3.5
#pragma debug
        ENDCG
    }
   
        FallBack "Legacy Shaders/Specular"
}

```



