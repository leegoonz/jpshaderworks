## Texture2LayerHeightBlending.Simple

```
void surf(Input IN, inout SurfaceOutput o) 
    {
        fixed2    uv0 = IN.uv_MainTex;
        fixed2    uv1 = IN.uv_MainTex2;
        fixed4    paramtex = tex2D(_ParamTex, uv0);
        fixed4    mainTex = tex2D(_MainTex, uv0);
        fixed4    mainTex2 = tex2D(_MainTex2, uv1);
        fixed    clipTex = tex2D(_ClipTex, uv1).r;
        fixed factorRed = IN.color.r; // Get from vertex color input. (R) 
        fixed factorGreen = IN.color.g; // Get from vertex color input. (G) 
        fixed combinedMask = clamp(fPow(factorGreen + (clipTex * _BlendScale1), 2), 0, 1);
        fixed3 texCom = flerp(mainTex, mainTex2, combinedMask);
        paramtex = flerp(paramtex, fixed4(0,0,1,0), combinedMask);
        o.Albedo = texCom * paramtex.b;
        o.Gloss = paramtex.g;
        o.Specular = paramtex.r;

    }
```

---

## Texture2LayerHeightBlending.2NormalBleding

```
void surf(Input IN, inout SurfaceOutput o)
    {
        fixed2    uv0 = IN.uv_MainTex;
        fixed2    uv1 = IN.uv_MainTex2;
        fixed4    paramtex = tex2D(_ParamTex, uv0);
        fixed4    mainTex = tex2D(_MainTex, uv0) * _MainColor;
        fixed4    mainTexBump = tex2D(_BumpMap, uv0);
        fixed4    mainTex2 = tex2D(_MainTex2, uv1);
        fixed4    mainTexBump2 = tex2D(_BumpMap2, uv1);
        fixed    clipTex = tex2D(_ClipTex, uv1).r;
        fixed factorRed = IN.color.r; // Get from vertex color input. (R) 
        fixed factorGreen = IN.color.g; // Get from vertex color input. (G) 
        fixed combinedMask = clamp(fPow(factorGreen + (clipTex * _BlendScale1), 2), 0, 1);
        fixed3 texCom = flerp(mainTex, mainTex2, combinedMask);
        paramtex = flerp(paramtex, fixed4(0, 0, 1, 0), combinedMask);
        o.Albedo = texCom * paramtex.b;
        o.Gloss = paramtex.g;
        o.Specular = paramtex.r;
        o.Normal = fNormalBlender(mainTexBump , mainTexBump2 , IN.color, 1 - _BlendNormal);

    }
```

> ## Related code
>
> #### [Tweak shader function for the custom shaders.](/simple-shader-func.md)





