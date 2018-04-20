## TWEAK SHADER FUNCTION COLLECTION.

> ### Custom Normal Scale with Height data
>
> ```
> inline half3 UnpackNormalX2M(half2 packednormal)
> 		{
> 			#if _GLOBALSCALNORAL
> 				return normalize(half3(packednormal.xy * 2 - 1, clamp(1 - _BumpScale, 0.2, 0.8)));
> 			#else
> 				return normalize(half3(packednormal.xy * 2 - 1, 0.7));
> 			#endif
> 		}
> ```
>
> ### [https://youtu.be/VXH8r0BFdmU](https://youtu.be/VXH8r0BFdmU)
>
> ### 
>
> ### Custom Common Structure.
>
> ```
> struct v2f_pangu {
>     float2 uv    : TEXCOORD0;
> };
> ```
>
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
>
> ### Gamma to Linear approximation func.
>
> ```
> #define GAMMATOLINEARSIMPLE
> inline half3 GammaToLinearSimple(half3 sRGB)
> {
>     return sRGB * 0.19875h + (sRGB * sRGB ); //pow(sRGB , 2.2);
> }
> ```
>
> ### GammaToLinear Unity approximation
>
> ```
> inline half3 GammaToLinear(half3 sRGB)
> {
>     return sRGB * (sRGB * (sRGB * 0.305306011h + 0.682171111h) + 0.012522878h);
> }
> ```
>
> ### Fast Lerp
>
> ```
> //fast lerp simple function
> inline fixed flerp(fixed a, fixed b, fixed t)
> {
>     return a * (1 - t) + b * t;
> }
>
> inline fixed3 flerp(fixed3 a, fixed3 b, fixed t)
> {
>     return a * (1 - t) + b * t;
> }
>
> inline fixed4 flerp(fixed4 a, fixed4 b, fixed t)
> {
>     return a * (1 - t) + b * t;
> }
> ```
>
> ### NormalBlenderWithMask function.
>
> ```
> //Normal blender common function by jp shader function library 
> inline fixed3 fNormalBlender( fixed4 normalA , fixed4 normalB , fixed4 color , fixed blendScale)
> {
>     fixed4 nrm = normalA * (color.r + (1 - blendScale))  +  normalB * (color.g) ;
>     return UnpackNormal(normalize(nrm));
> }
> ```
>
> ### Fresnel limited approximation function
>
> ```
> //mobile friendly and stable code
> fixed jpFresnel = 1- max(min(dot(normalize(ViewDir), Normal), 1), 0);
> fixed rc = 5 * FresnelScale * (jpFresnel * jpFresnel * 0.05);
> return rc + rc;
> ```

![](/assets/20170223_001128.jpg)&lt;jpFresnel&gt;

related blog : [http://www.leegoonz.com/single-post/2017/02/22/Custom-Mobile-stable-and-fast-Fresnel-constant-approximation](http://www.leegoonz.com/single-post/2017/02/22/Custom-Mobile-stable-and-fast-Fresnel-constant-approximation)

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

Terrain RGB Blending assistant function.

### Edge Softness modulate blending.

```
factorRed = 1.0 - factorBlue - factorGreen;
```

> ### sRGB Splat Edge Correct.
>
> ```
> fixed correctionFactor = 1 / (factorRed + factorGreen + factorBlue);
> ```
>
> Why those it need for...
>
> Reproduce of basic problem.
>
> Below case is sGRB flag mode on the LinearSpace rendering.![](/assets/20170307_215248.jpg)\#Blending area some darker.
>
> ShaderForge using node.![](/assets/sf_rgb_blendingcorrection_372017.jpg)This case that just for state of the sRGB splat map mode as not linear space.
>
> Using custom code in ShaderForge.![](/assets/sf_rgb_blendingcorrection_372018.jpg)
>
> Also... If you already to used to linear space based splat map those it exactly useless but When you developed to low-end devices as for the mobile project it seems like I guess useful.o Int optimal basic for the correction code at above.



