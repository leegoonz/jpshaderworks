

![](/assets/2018-09-02_1-38-55.png)![](/assets/2018-09-02_1-39-32.png)

```ruby
Shader "X2M/Character/Eyeball fastmode" {
	Properties{
	  _IrisScale("Scale", Range(.5, 1.5)) = 1
	  _Dilation("Dilation", Range(-.5, 2)) = 0
	  _SpecStr("Specular power", Range(0, 10)) = 1
      _SpecPower ("Specular Glossy", Range (32, 1024)) = 512
	  _SpecCustomDirection("Specular Light Direction 0", Vector) = (0.3,-0.45,0,0)
      _Color ("Iris Tint", Color) = (1,1,1,1)
      _MainTex ("Eye Texture", 2D) = "white" {}
	  [HDR]_Cube("Reflection Cubemap", Cube) = "" {  }
	  _ReflectionScale("Reflecion Scale", Range(0, 1)) = 1
    }

    SubShader {
    	Tags { "RenderType"="Opaque" "Queue"="Geometry" }
        Pass {
			Tags {"LightMode" = "ForwardBase"}
		    CGPROGRAM
		        #pragma vertex vert
		        #pragma fragment frag
				#include "UnityCG.cginc"
		        #include "AutoLight.cginc"
				#pragma multi_compile_fwdbase
		
		        struct v2f
		        {
		            float4 pos : SV_POSITION;
					float2 uv : TEXCOORD0;
					fixed3 lightDir : TEXCOORD1;
					fixed3 vlight : TEXCOORD2;
					float3 viewDir : TEXCOORD3;
					float3 viewDirW : TEXCOORD4;
					float3 tangent : TEXCOORD5;
					float3 binormal : TEXCOORD6;
					float3 normal : TEXCOORD7;
					LIGHTING_COORDS(8,9) 
					
		        }; 
				
				fixed _IrisScale;
				
		        v2f vert (appdata_tan v)
		        {
		            v2f o;
		            o.pos = UnityObjectToClipPos(v.vertex);
					float2 uv = v.texcoord.xy - .5;
					uv *= _IrisScale;
					o.uv = uv + .5;
					float3 viewDir = -ObjSpaceViewDir(v.vertex);
					float3 worldRefl = mul((float3x3)unity_ObjectToWorld, viewDir);
					TANGENT_SPACE_ROTATION;
					o.lightDir = mul (rotation, ObjSpaceLightDir(v.vertex));
					o.viewDir = mul(rotation, ObjSpaceViewDir(v.vertex));
					o.viewDirW = WorldSpaceViewDir(v.vertex);
					
					o.normal = normalize(mul(float4(v.normal, 0.0), unity_WorldToObject).xyz);
					o.tangent = normalize(mul(unity_ObjectToWorld, v.tangent).xyz);
					o.binormal = cross(o.normal, o.tangent) * v.tangent.w;
					
					float3 worldN = mul((float3x3)unity_ObjectToWorld, SCALED_NORMAL);
				    float3 shlight = ShadeSH9 (float4(worldN,1.0));
				  	o.vlight = shlight;
		            TRANSFER_VERTEX_TO_FRAGMENT(o);
		            return o;
		        }
		
		        sampler2D _MainTex;
		        sampler2D _Masks;
		        samplerCUBE _Cube;
				half _ReflectionScale;
		        half4 _Color;
		        half4 _LightColor0;
		        half _SpecStr;
		        half _SpecPower;
		        half _Dilation;
				half2 _SpecCustomDirection;
				
		        
		        float4 frag(v2f i) : COLOR
		        {
		        	half3 tweakLightDir = i.lightDir + half3(_SpecCustomDirection , 0);
					half3 nViewDirT = normalize(i.viewDir);
		            half3 h = normalize(tweakLightDir + nViewDirT);
					half2 uv = i.uv - .5;
		            half pupil = saturate(length(uv)/ 0.14);
					uv *= lerp(1.0, pupil, _Dilation);
		            uv += .5;
		
					fixed4 albido = tex2D(_MainTex, uv);
						
					albido.rgb *= (_Color * (albido.a * 2)) + (1- albido.a);
		        	half3 n = half3(0.5,0.5,1);
		        	half3 n2 = half3(0.05,0.05,1);
					float NdotL = max(0,dot(n,tweakLightDir));
		            float NdotH = max(0,dot(n, h));
		            float N2dotH = max(0,dot(n2, h));
					float N2dotV = 1.0 - saturate(dot(normalize(i.viewDir), n2));
					float atten = LIGHT_ATTENUATION(i);
		            float3 spec = pow(NdotH, 10) * 0.01 * _LightColor0.rgb * atten;
		            spec += pow(N2dotH, _SpecPower) * _SpecStr * _LightColor0.rgb;
					
					float3 normalW = (i.tangent * n2.x) + (i.binormal * n2.y) + (i.normal * n2.z);
					float3 reflection = texCUBE(_Cube, reflect(-i.viewDirW, normalW)) * N2dotV;
					
		            
		            fixed4 c;
		            c.rgb = ((albido * _LightColor0.rgb * atten * NdotL)  + spec)  + ((reflection * _ReflectionScale) * albido.a) ;
					c.rgb += albido * i.vlight;
		            c.a = 1;
		            return c;
		        }
		    ENDCG
		}
      }
    Fallback "Diffuse"
}
```



