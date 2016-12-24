# Add to UV2 texcoord into surface shader

```
SubShader 
{
Tags { "RenderType"="Opaque" }
LOD 200

CGPROGRAM
#pragma target 3.0
#pragma surface surf Lambert vertex:vert

sampler2D _MainTex;

struct Input 
{
    float2 uv_MainTex;
    float2 addUV2;
};

void vert (inout appdata_full v, out Input o) 
{
    o.addUV2 = v.texcoord1.xy;
};


void surf (Input IN, inout SurfaceOutput o)
{ 
    float2 uv2 = IN.addUV2;
}
ENDCG
}
```



