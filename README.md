# Universal_Driver_29_X
Newest version of the Universal Driver 2.x series DRSSTC driver with Pulse Skip functionality and System Extentions




OVERVIEW:   The UD29X stands for the Universal Driver (UD) 2.9 eXtensible. It is based on the DRSSTC driver series originally designed by Steve Ward, and optimized over time by various coilers around the world. This driver was designed from scratch using notes from various documented builds over the years, and is being sold with permission of the original designer, Steve Ward. At its heart, the UD29X is essentially a standard SMD UD like the UD2.7C but with a few major and minor differences:

   Pulse skip functionality from the THT UD2.9 by Daniel Marks (profdc9 on github) added.
   Breakout headers instead of LEDs for extension options (more on this later.)
   Compact / low component count design (solder jumpers instead of 2 pin headers, one gate drive channel (2 dual channel MOSFETs))
   UVLO set with a resistor instead of tune pot. (Not usually necessary to tune)
   Single ST style HFBR-2412T or OPF-2412T fiberoptic receiver instead of IF option. (Most commonly used – Extensions may be added later for IF series)
   24 VDC input only (No bridge rectifier. Use normal 24V 2-5A switching power supply)
   SMD burden resistors (on bottom of board)  

For in-depth information on the UD series functionality in general, phase lead tuning, and parts, world renown coiler Gao Guangyan over at loneoceans.com/labs/ud27/ has an amazing write-up and very detailed document explaining the inner workings and theory of the UD driver operation and tuning. This documentation here will provide getting started and functional information and reference for the UD29X itself relevant to using the system and the extensions available.

SPECS:  

    Expansion headers for external system development
    Pulse Skip mode
    Over Current Detection (OCD) mode
    Phase lead compensation for soft switching
    24V dual push-pull MOSFET gate drive
    Undervoltage lockout (UVLO)
    Phase reverse jumpers
    Polar connectors for safety and proper phasing
    Thermal layout for voltage regulators

EXTENSIONS:

The 29X, while being a fully functional standalone Universal Driver, was originally designed as a platform for the continual design and development of more in-depth DRSSTC controllers using cheap and easily available microcontrollers or other simple circuits that can be built and attached directly to the driver. Extensions of the system can include simple board configurations like other fiberoptic receivers or interrupter input types, LEDs, etc. to much more complex systems like my sigma-delta QCW driver extension and other adaptive current modulation systems, real time telemetry, safety features such as on-time limiting, temperature and other sensors for system diagnostics, and even completely new sensor systems like automatic primary strike detection. As I design new extensions, I will be documenting and posting them. I hope to see others use this platform to continually push Tesla technology forward, as this was a major motivation to create this project.

PULSE SKIP AND OCD:  

OCD mode is a useful safety feature for making sure your coil runs within a predefined primary current limit. If the current across the primary exceeds a current threshold, the pulse ends and goes back to steady state until the next interrupter pulse occurs. This can be a very useful tool in DRSSTC design to make sure you don’t exceed currents that can damage your bridge or blow up your IGBTs.   Pulse skip mode allows you to limit your peak current values without killing the pulse completely by skipping a single oscillation on the bridge during operation. This allows you to have increasingly long pulse durations (100s of uS to multiple mS) without exceeding the primary current you set. This feature allows you to maximize the overall power of the coil without jeopardizing your bridge with excessively high peak currents.

By default the UD29X is set to OCD mode. To enable Pulse Skip – Simply close the solder jumper on the back of the board labeled (SKIP_EN). The driver will now be in pulse skip mode!   Setting up the OCD and Pulse Skip – The upper current threshold is set by adjusting the tunable potentiometer on the board. First power the driver and attach a multimeter to the OCD_REF and GND pins on the breakout headers. As you turn the potentiometer you should see the voltage changing. This voltage is your upper limit current threshold reference. If the voltage through your current transformer (across the burden resistor) exceeds this voltage, the driver will go into OCD or Pulse Skip mode (depending on what you have configured). The LM311D used in this design is capable of comparing up to 12V at its highest threshold. Try to build your CT with the goal of staying within the 2V – 10V range here. See current transformer calculations below for more info on how to set these values.

When using pulse skip functionality, be careful to calculate your on times, Ipk, and other operational characteristics and compare them to your coil specs. Exceeding MMC RMS currents, thermal dissipation capacity of the IGBTs, RMS through rectifiers and capacitors, etc can happen quickly and ruin your expensive power components. Also be careful with high BPS and make sure to calculate your duty cycle. Playing with dials can quickly turn into accidentally running the coil close to CW operation! In short, make sure you are running the coil within reasonable limits or you may kill some pricey parts. Pulse skipping is extremely powerful. That said, pulse skipped arcs are magnificent and a great way to create some intense arcs.

It is always a good idea to measure your Ipk and compare it to your calculated values for accuracy. A CT with burden resistor and a BNC connector on a ~10’ (~3m) BNC cable connected to an oscilloscope can be quite useful here. I have these for sale in my shop.  

PHASE LEAD COMPENSATION:  

Loneoceans has a great guide for tuning phase lead on a UD in his guide at https://www.loneoceans.com/labs/ud27/ in the “Tuning Phase Lead on the UD2.7” section.

For inductor selection, a quick excerpt from loneoceans - "These 7M3-series tunable inductors for phase lead adjustment can be bought or sampled from www.coilcraft.com. Inductance values varies depending on your switch and switching frequency. Typical values include: 7M3-123 (9 - 15uH; works well with TO247 IGBTs), 7M3-153 (11 to 19uH), going up through 7M3-223 (17-28uH), 333 (25-41uH), 393 (29-49uH; works well with CM200/300s), 563 (42 - 70uH) etc.  Use a flat-head non-magnetic or plastic screwdriver for tuning (see instructions below in this page)."

UNDERVOLTAGE LOCKOUT:  

The UVLO section in the 29X uses a MC34164SN UVLO chip with an internal reference voltage  of 4.33V. The 24V power input goes through a voltage divider that feeds into the UVLO chip. If the voltage across the divider drops below 4.33V, the driver shuts itself off. This feature is very useful for making sure your driver isn't under powering the gates and causing switching issues if the driver voltage drops too low.   The output voltage of the voltage divider can be calculated using ohms law and testing different resistor values. I use a 1K resistor on R_UVLO, which gives a cutoff of ~22.8V. Anything below ~1.13KR can be used here depending on desired sensitivity. If you aren't sure, stick with the 1K.

The UVLO functionality can be enabled or disabled by soldering the UVLO_EN jumper in the UVLO section of the board.  

CTs, GDT AND PHASING:  

One of the first things to do when designing your setup for a DRSSTC is to calculate and build your CTs and burden resistors. The drivers come with 5.1R burden resistors installed. These should be fine for most use cases but can be swapped if desired. As a rule of thumb it is best to keep currents around 1A at the max current you plan to see through your primary. The example configs. for current transformers below are a useful starting point.

Example configurations: For simplicity in 100V increments, these are my go-to's.

    0 - 250 A(peak)      1:16:16 = 1:256      ~2V / 100A
    250 - 500 A(peak)    1:23:22 = 1:506        ~1V / 100A
    500 - 1000 A(peak)   1:32:32 = 1:1024      ~0.5V / 100A

If using pulse skip or OCD, your OCD CT should be wound the same as your Feedback CT. The ends of the CTs and GDT should be terminated with a female 2 pin polarized MOLEX connector to plug directly into the board.

The OCD and feedback transformers require no phasing themselves, so you can just plug them directly into the board. The OCD CT has a rectified output that is fed into the OCD voltage reference, so it doesn't have a phase necessarily. The feedback does however have to be properly in phase with the driver output. This phasing can be flipped by setting the two sided solder jumper on the bottom of the board.   The GDT output should easily provide sufficient current for most moderately sized coils, but the single output design is not recommended for IGBTs larger than CM300. It may very well work for larger bricks, but this will depend on the gate charge and frequency as well as your GDT setup itself. The coil does have UVLO which should add some safety barrier here, but use of the driver with large coils has not been tested and is done under your own discretion.  

OTHER BOARD CONFIGS:

C22 is the C33 from loneoceans documentation. The value of the cap should be set for the frequency the board is expected to operate. Example values are:

    2.2nF (<100KHz)
    1nF (100 - 200KHz)
    <1nF (>200KHz)

