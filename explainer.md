# Allow calc function infinity and NaN
Author: [Seokho Song](https://github.com/devsdk)(0xdevssh@gmail.com)

## Summary

 According to the CSS specification [CSS Values and Units Module Level 4](https://drafts.csswg.org/css-values/#calc-type-checking), CSS calc() math function should allow infinity and NaN values by 'infinity', '-infinity', 'NaN' keywords or expressions that could be evaluated into infinity or NaN such as 'calc(1/0)'.

## Infinity and NaN operations

The infinity and NaN will be evaluated by [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754). 

The operations: 
| Operation                                     | Result    |
|-----------------------------------------------|-----------|
| n ÷ ±infinity                                 | 0         |
| ±infinity × ±infinity                         | ±infinity |
| ±n ÷ ±0  (n != 0)                             | ±infinity |
| ±n × ±infinity (n is finite, n != 0)          | ±infinity |
| infinity + infinity<br>infinity – -infinity   | +infinity |
| -infinity – infinity<br>  -infinity + –infinity | -infinity |
| ±0 ÷ ±0                                       | NaN       |
| ±infinity ÷ ±infinity                         | NaN       |
| ±infinity × 0                                 | NaN       |

## Implementation aspect

 All of the values evaluated in infinity or NaN should be clamped to ideal finite values before consuming to components.
 
 According to the [spec](https://www.w3.org/TR/css-values-4/#top-level-calculation), It says "If a top-level calculation (a math function not nested inside of another math function) would produce a value whose numeric part is NaN, it instead act as though the numeric part is +∞."
    Therefore NaN will be calculated as positive infinity.

## Examples
 Below infinity and NaN could be evaluated by the expression such as Zero Division. The result of using the expression and keywords is identical.

1. **\<length> and \<length-percentage>**
    ```CSS
    div {
        width:calc(infinity*1px);
        height:10px;
        background-color:green;
    }
    ```

    The maximum value will be calculated by [INT_MAX / kFixedPointDenominator](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/platform/geometry/layout_unit.h;l=61-62;drc=5a482950b21c31f5d80b7809ca248a94dea0e0d2) / [zoom factor](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/layout/adjust_for_absolute_zoom.h;l=67;drc=a820f380187c1c0ddaf645e5e71f914a12653a7e) (≈ 3.35544e+07 if zoom factor is 1).

    This case div's computed width will be a maximum value of computed style.
     In case of NaN:
     ``` CSS
        width:calc(NaN*1px);
     ```
    The value will be a maximum value of computed style to indicate positive infinity.

2. **\<time>**
   ```CSS
    div {
        animation-duration: calc(infinity*1s);
        animation-name: SomeAnimation;
    }
   ```
    This case div's computed duration will be maximum value(≈1.79769e+308s) of computed style.

     In case of NaN:
     ``` CSS
        animation-duration:calc(NaN*1s);
     ```
     The value will be a maximum value(≈1.79769e+308s) of computed style to indicate infinity.

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

    In this case, the calc produces for \<angle> is a specific constant value (2867080569122160) which is the nearest calculatable double maximum value in the current implementation.  Details are explained in design docs. Please see below.

### See Also

**Design Doc:** [doc](https://docs.google.com/document/d/1kksm8aa5HpCph5NmJEwrCrj2e3p85RORQCr7OSsWAOs/edit?usp=sharing)

**Issue:** [1133390](https://crbug.com/1133390)
