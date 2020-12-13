# Allow calc function infinity and NaN
Author: [Seokho Song](https://github.com/devsdk)(0xdevssh@gmail.com)

---
## Summary

 According to the CSS specification [CSS Values and Units Module Level 4](https://drafts.csswg.org/css-values/#calc-type-checking), CSS calc() math function should allow infinity and NaN values by 'infinity', '-infinity', 'NaN' keywords or expressions that could be evaluated into infinity or NaN such as 'calc(1/0)'.

## Infinity operations

The infinity and NaN will be evaluated by [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754). 


## Implementation aspect

 All of the values evaluated in infinity or NaN should be clamped to ideal finite values before consuming to components.

## Examples
 Below infinity and NaN could be evaluated by the expression such as Zero Division. The result of using the expression and keywords is identical.

1. **\<length> and \<length-percentage>**
    ```CSS
    div {
        width:clac(infinity*1px);
        height:10px;
        background-color:green;
    }
    ```
    
    This case div's computed width will be a maximum value(≈1.79769e+308) of double type.
     
     In case of NaN:
     ``` CSS
        width:clac(NaN*1px);
     ```
     The value will be a maximum value(≈1.79769e+308) of double to indicate infinity.

2. **\<time>**
   ```CSS
    div {
        animation-duration: calc(infinity*1s);
        animation-name: SomeAnimation;
    }
   ```
    This case div's computed duration will be maximum value(≈1.79769e+308) of double type.
    
     
     In case of NaN:
     ``` CSS
        animation-duration:clac(NaN*1s);
     ```
     The value will be a maximum value(≈1.79769e+308) of double to indicate infinity.

3. **\<angle>**
   ```CSS
    div {
        transform: rotate(calc(infinity*1deg));
    }
   ```
    ```CSS
    div {
        transform: rotate(calc(NaN*1deg));
    }
   ```
    This case div's computed rotation will be 0 for infinity, -infinity and NaN by clamping.

4. **Interpolation** ~ WIP
  Strictly followed the spec, It's interpolated by enormous clamped values. However, this may need discussion. 

## See Also
**Design Doc:** [doc](https://docs.google.com/document/d/1kksm8aa5HpCph5NmJEwrCrj2e3p85RORQCr7OSsWAOs/edit#heading=h.w4xnn01mqy78)
**Issue:** [1133390](https://crbug.com/1133390)