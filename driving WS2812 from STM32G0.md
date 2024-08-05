## driving WS2812 smart LEDs from STM32G031 in a dip package
using TIM PWM + DMA + mem-mem copy

### references
excellent WS2812 reference: [WS2812 NeoPixels are not so finicky once you get to know them](https://wp.josh.com/2014/05/13/ws2812-neopixels-are-not-so-finicky-once-you-get-to-know-them/).  
[NeoPixels Revealed: Why you should give your bits room to breathe](https://wp.josh.com/2014/05/16/why-you-should-give-your-neopixel-bits-room-to-breathe/).  

good source of WS2812 LED stings in various size and shapes (formerly "Mr Right LEDs")  https://wixure.aliexpress.com/store/1100766527 .  

#### WS2815 datasheet timing
T0H = 0.35 uS  
T0L = 0.80 uS  
    total 0 = 1.15 uS  
T1H = 0.70 uS  
T1L = 0.60 uS  
    total 1 = 1.3 uS  
reset > 50.0 uS.  


Timer + DMA-ch1 are configured to drive compare out with 0/1 per bit value for 8 bits (one R or G or B value).  
When DMA-ch1 is complete, mem-mem DMA is started for next 8 bit from precomputed values in flash to RAM buffer.
When, mem-mem DMA is complete, DMA-ch1 restarts  

TIM ch is configured in compare mode

flash table: 8 entries * 256 values = 2K table.
ram table:  3 x 8 entries. = 24 bytes.

1) start DMA 1 of 3 * 8 bits
2) DMA  1 complete, memcpy next 3 blocks of 8 bytes each from flash to RAM.
