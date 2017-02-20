## FOLIAGE SHADER FAMILY

##### Explaining movie clip here.

[http://www.leegoonz.com/single-post/2017/02/21/Vertex-normal-tweaking-for-tree-of-leaf-for-Unity3D](http://www.leegoonz.com/single-post/2017/02/21/Vertex-normal-tweaking-for-tree-of-leaf-for-Unity3D)

#### TreeLeaf \(no lightmap\) : Just cast shadow for Lightmap calculation.

```
Shader "Tianyu Shaders/Scene/Foliage/AlphaTest/TreeLeaf(no LM)"
{
    Properties{
        _AlbedoExposure("Albedo Intensity", Range(0.25,2)) = 1
        _Color("Main Color", Color) = (1,1,1,1)
        _MainTex("Base (RGB)", 2D) = "white" {}
    //We already decided about split to alpha source because pvrtc compress it have some
    //problem as block compress. Apple they suggest to split to alpha source then much better with pvrtc on mobile devices.
    //It is official issue of pvrtc format.
        _AlphaTex("AlphaMap (A)", 2D) = "white" {}
        _Cutoff("Alpha cutoff", Range(0,1)) = 0.5
    }

        SubShader{
        Tags{ "Queue" = "AlphaTest" "IgnoreProjector" = "True" "RenderType" = "TransparentCutout" "PerformanceChecks" = "False" }
        LOD 200

        CGPROGRAM
#pragma surface surf Lambert exclude_path:prepass alphatest:_Cutoff nolightmap nodynlightmap nodirlightmap vertex:vert  nometa //noforwardadd
#pragma only_renderers d3d11 glcore gles gles3 metal
#pragma fragmentoption ARB_precision_hint_fastest
#pragma debug
    sampler2D _MainTex;
    sampler2D _AlphaTex;
    fixed _AlbedoExposure;
    fixed4 _Color;


    struct Input {

        float2 uv_MainTex;
        fixed4    color; //Vertex color struct data.
        INTERNAL_DATA
    };

    void vert(inout appdata_full v, out Input o) {

        UNITY_INITIALIZE_OUTPUT(Input, o);
        o.color = v.color;
        o.color.a = v.color.a;

    }

    void surf(Input IN, inout SurfaceOutput o) {
        fixed4 vColor = IN.color;
        fixed4 c = tex2D(_MainTex, IN.uv_MainTex);
        fixed alpha = tex2D(_AlphaTex, IN.uv_MainTex);
        o.Albedo = c.rgb * _Color * (vColor.rgb + 1 - vColor.a) * _AlbedoExposure;
        o.Alpha = alpha;
    }
    ENDCG
    }

        Fallback "Legacy Shaders/Transparent/Cutout/VertexLit"
}
```



