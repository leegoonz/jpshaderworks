### TextureLayerHeightBlending

```
void surf(Input IN, inout SurfaceOutput o) 
    {
		fixed2	uv0 = IN.uv_MainTex;
		fixed2	uv1 = IN.uv_MainTex2;
		fixed4	paramtex = tex2D(_ParamTex, uv0);
        fixed4	mainTex = tex2D(_MainTex, uv0);
		fixed4	mainTex2 = tex2D(_MainTex2, uv1);
		fixed	clipTex = tex2D(_ClipTex, uv1).r;
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


