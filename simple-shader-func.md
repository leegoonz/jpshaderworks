## SIMPLE SHADER FUCN COLLECTION.

### Stable code of fresnel func.

```
#if _HITEFFECT_ON
        half rimFactor = 1.0 - max(min(dot(normalize(IN.viewDir), o.Normal), 1), 0); //This code type is more benefit to use PowerVR chipset as iPhone devices.
        half4 r_c = ((rimFactor * rimFactor) + (rimFactor * (rimFactor * 0.425))) * _RimPower; //Pow function optimize simplified math 
        o.Albedo += r_c;
#else
        o.Albedo;
#endif
```

### Pow approximation func.

```
#define GAMMATOLINEARSIMPLE
inline half3 GammaToLinearSimple(half3 sRGB){
sRGB =  ( sRGB * 0.19875) + (sRGB * diffuseTex); //pow(diffuseTex , 2.2);
return sRGB;
}
```



