![Status](https://img.shields.io/badge/STATUS-OPERATIONAL-green?style=for-the-badge&logo=server)
![KiCad](https://img.shields.io/badge/KiCad-yellow?style=for-the-badge&logo=kicad&logoColor=white)
![AllWinner](https://img.shields.io/badge/SoC-H616-009688?style=for-the-badge&logo=allwinner&logoColor=white)
![Telegram](https://img.shields.io/badge/BuildRoot-2CA5E0?style=for-the-badge&logo=linux&logoColor=white)
![AI](https://img.shields.io/badge/JLCPCB-purple?style=for-the-badge&logo=PCB)
# Nebula board


A brief description of my Allwinner H616 board.
A minimalist board measuring 50 mm by 35 mm (subject to change)
# JLCPCB & LCSC
The main challenge with this 8-layer board is using an available stackup from JLCPCB as of August 29, 2026, I recommend [**JLC08101H-1080 (/Finished thickness 1.02 mm ±10%**.](https://jlcpcb.com/pcb-impedance-calculator)
  
Another challenge is that the via holes are 0.3 mm in size (0.35/0.45 mm). 
With these parameters, [JLCPCB](https://cart.jlcpcb.com/) allows you to manufacture the board for $2 USD. Any change will make the price astronomical.
For example, it is not recommended to change the **minimum via hole size/diameter.**
For via covering, I used “via in pad,” so the manufacturer does not allow the via to remain unfilled with epoxy. 
Dimensions up to 50 mm by 40 mm 
If you have the budget, I recommend simply assembling a board based on the original  [Kononenko-K](https://github.com/Kononenko-K/Allwinner_H616_Devboard) design or redesigning my board, because there’s no point in doing this if you don’t want to order a PCB at a promotional price like I did—you’ll waste a lot of energy and may sacrifice stability.

PCB assembly, LCSC isn’t supported in Ukraine, of course, you can use a mail forwarding service, but I’m interested in soldering the board myself and buying parts cheaper on marketplaces—and that same **V3S** costs me practically nothing on the market. If you’re making a board for PCB assembly, it’s worth making it single-sided to save money, but the economical PCBA mode isn’t supported for BGA soldering, and it costs $25, so there’s no point in doing that.
# RAM
My board has only one DDR3 chip, up to 1 GB, but if you’re looking for performance, two chips provide twice as much memory and twice the speed.


It's important to note that the H616 supports 16- or 32-bit RAM; according to **JEDEC**, a single DDR3 chip has a maximum of ***16 bits, so you'll need 16-bit RAM specifically.***

### SK Hynix  
* H5TQ1G63BFR (1 Gb / 128 MB)  
* H5TQ2G63BFR / H5TQ2G63DFR (2 Gb / 256 MB)  
* H5TQ4G63AFR / H5TQ4G63CFR / H5TQ4G63MFR (4 Gb / 512 MB)  
* H5TC8G63AMR / H5TC8G63CMR (8 Gb / 1 GB — low-voltage DDR3L)  
### Samsung  
* K4B1G1646G / K4B1G1646I (1 Gb / 128 MB)  
* K4B2G1646E / K4B2G1646F (2 Gb / 256 MB)  
* K4B4G1646D / K4B4G1646E (4 Gb / 512 MB)  
* K4B8G1646D / K4B8G1646B (8 Gb / 1 GB)  
### Micron  
* MT41J64M16 (1 Gb / 128 MB)  
* MT41J128M16 / MT41K128M16 (2 Gb / 256 MB)  
* MT41J256M16 / MT41K256M16 (4 Gb / 512 MB)  
* MT41J512M16 / MT41K512M16 (8 Gb / 1 GB)  
### Nanya  
* NT5CB64M16 (1 Gb / 128 MB)  
* NT5CB128M16 / NT5CC128M16 (2 Gb / 256 MB)  
* NT5CB256M16 / NT5CC256M16 (4 Gb / 512 MB)  
* NT5CC512M16 (8 Gb / 1 GB)**


## EDA
I’m screwing around with the layout because the DDR was really hard to route, and I lost all motivation working in KiCad—the wise will skip KiCad and download Altium, so think carefully about which software to use. 
## Connection
For Wi-Fi/Bluetooth, I’d choose a board based on the **RTL8821CS** for better speed and so on, because Wi-Fi connects via GPIO (i.e., **SDIO**) and Bluetooth via **UART**. But since there are 3 free USB channels, I’ll connect an **RTL8821CU** board to them for a simple USB connection. Feel free to use any other wireless module that suits your needs; **5 (5.8) GHz** support is important to me, which is why I chose the RTL8821CU, but I recommend taking a closer look at Broadcom options.

## Power

Since I’m planning for standalone operation, and the PMIC **AXP305** wasn’t designed for that—only for monotonous operation from a power supply in TV set-top boxes—it doesn’t support a battery, and there’s certainly no datasheet for it. According to the developer, we’re supposed to use only the reference design together with the **H616**—so be it. 

The **SW6206** is a versatile “all-in-one” microcontroller for power banks that supports fast charging up to 22.5 W. It supports simultaneous operation of up to 5 ports and is compatible with nearly all modern power delivery protocols, including ***PD 3.0, QC 4+, and PPS.***
Therefore, if you plan to run the device on battery power, one USB port is dedicated exclusively to charging; it is not used for data transmission with the board, since smart charging protocols like QC and PD require a data line in the cable.
 # Flux & Paste

For *double-sided soldering*, I recommend purchasing solder pastes: low-temperature (bismuth-based, **~138°C)** and medium-/high-temperature (lead-based, **~183°C**, or lead-free, **~217°C**).

**MECHANIC V5B45** Sn42/Bi58 Solder Paste: Composition and Temperature: Tin-bismuth alloy (Sn42/Bi58) with a precise melting point of **138°C**. For the top side of **BGAs**.

**BS458** Mechanic Solder Paste: Features: A low-melting-point, lead-free, RoHS-compliant formulation supplied in convenient jars. For the back side, resistors, and capacitors.


# Documentation & Soldering      WARNING!!!
[***This***](https://csbible.com/wp-content/uploads/2018/03/CSB_Pew_Bible_2nd_Printing.pdf)

<p align="right">
  <img src="https://komarev.com/ghpvc/?username=Bogdan8266&page=h616-board&color=blue&label=Repo%20views" alt="Views">
</p>
