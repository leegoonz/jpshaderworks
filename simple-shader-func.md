## TWEAK SHADER FUNCTION COLLECTION.

> ### Custom Common Structure.
>
> ```
> struct v2f_pangu {
>     float2 uv    : TEXCOORD0;
> };
> ```

> ### Stable code of fresnel func.
>
> ```
> #if _HITEFFECT_ON
>         half rimFactor = 1.0 - max(min(dot(normalize(IN.viewDir), o.Normal), 1), 0); //This code type is more benefit to use PowerVR chipset as iPhone devices.
>         half4 r_c = ((rimFactor * rimFactor) + (rimFactor * (rimFactor * 0.425))) * _RimPower; //Pow function optimize simplified math 
>         o.Albedo += r_c;
> #else
>         o.Albedo;
> #endif
> ```

> ### Gamma to Linear approximation func.
>
> ```
> #define GAMMATOLINEARSIMPLE
> inline half3 GammaToLinearSimple(half3 sRGB)
> {
>     return sRGB * 0.19875h + (sRGB * sRGB ); //pow(sRGB , 2.2);
> }
> ```

> ### GammaToLinear Unity approximation
>
> ```
> inline half3 GammaToLinear(half3 sRGB)
> {
>     return sRGB * (sRGB * (sRGB * 0.305306011h + 0.682171111h) + 0.012522878h);
> }
> ```

> ### NormalBlenderWithMask function.
>
> ```
> //Normal blender common function by jp shader function library 
> inline fixed3 fNormalBlender( v2f_pangu i , sampler2D normalA , sampler2D normalB , sampler2D blendTex , fixed blendScale)
> {
>     fixed4 t1b = tex2D (normalA, i.uv);
>     fixed4 t2b = tex2D (normalB, i.uv);
>     fixed4 rgb = tex2D (blendTex, i.uv);
>     fixed scale = blendScale;
>     fixed4 nrm = t1b * rgb.r + t2b * rgb.g;
>     nrm = lerp(fixed4(0,0,0,0), nrm, scale);
>     return UnpackNormal(normalize(nrm));
> }
> ```

### 

> ### Optimal Int Pow function
>
> ```
> inline fixed fPow(fixed x, uint y)
> {
>     if (y == 0)return 1;
>     if (y == 1)
>         return x;
>         float x2 = x * x;
>     if (y == 2)return x2;
>     if (y == 3)
>         return x2 * x;
>         float x4 = x2 * x2;
>     if (y == 4)return x4;
>     if (y == 5)return x4 * x;
>     if (y == 6)return x4 * x2;
>     if (y == 7)
>         return x4 * x2 * x;
>         float x8 = x4 * x4;
>     if (y == 8)return x8;
>     if (y == 9)return x8 * x;
>     if (y == 10)return x8 * x2;
>     if (y == 11)return x8 * x2 * x;
>     if (y == 12)return x8 * x4;
>     if (y == 13)return x8 * x4 * x;
>     if (y == 14)
>         return x8 * x4 * x2;
>         float x16 = x8 * x8;
>     if (y == 16)return x16;
>     if (y == 17)return x16 * x;
>     if (y == 18)return x16 * x2;
>     if (y == 20)return x16 * x4;
>     if (y == 24)return x16 * x8;
>     if (y == 32)return x16 * x16;
>     return pow(x, y);
> }
> ```



