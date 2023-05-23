# RxTx_WLAN_booster
EP-AB003 WLAN booster mod for separate Rx/Tx ports
heinrich.diesinger@gmx.net, F4HYZ

Today i decided to void the warranty of one of my WiFi boosters. It is a Edup EP-AB003, and has realistically 4..5 W output power although advertized as 8W. Its intended purpose is to be hooked up between a WLAN router and the antenna and it runs in half duplex mode. It has auto Rx/Tx switching by carrier detection and switches to Tx mode when the input power exceeds 3..5 dBm according to the manufacturer. 

![ep-ab003_hookup](https://user-images.githubusercontent.com/96028811/233800172-9518cb59-a7c8-4ad1-9c91-9f954610669c.jpg)

The intended use as a WiFi booster is actually prohibited in most countries due to EIRP restrictions. In the United States a higher limit is authorized than in Europe. Nevertheless the device is popular among ham radio operators as amplifier for Satcom using the QO-100 satellite. Mass produced for consumer market it is a lowcost alternative to dedicated ham radio amplifiers.

In contrast to the QO-100 configuration, here in this application note, the amplifier shall be modified for the particular use case where we want to transmit and receive through the same antenna at the same 2.4 GHz frequency with a SDR transceiver (e.g. Adalm Pluto), that has distinct ports for Rx and Tx. Therefore, we somehow need to connect the distinct Rx and Tx ports of our Adalm Pluto to the amplifier. Unfortunately our WLAN booster has only one switched port, so we will add another one to hook up our Pluto SDR transceiver.

![alternative_setup](https://user-images.githubusercontent.com/96028811/233623075-46de323f-067d-4cb7-bbc3-eeb35f3bb465.jpg)


Today's modification is different from the typical QO-100 amateur satcom use: This satellite has different up-and downlink frequencies and the amplifier is used in the transmit path only. The receive path consists of a LNB that downconverts the 10489 MHz to 739 MHz and is connected to the receive input of a transceiver or SDR. Such setups are described by many ham operators. Links:

http://afu-varel.dast.net/dl6ycl-via-qo100/

https://amateurfunkpraxis.de/qo100.html

https://twitter.com/ea2cq/status/1123140949805338624

![usual_qo100_config](https://user-images.githubusercontent.com/96028811/231276602-08e6c666-9b68-42cc-bfd7-4a939a21beac.jpg)


The amplifier has a Rx and a Tx path, lower and upper paths respectively, and multiplexer switches near the connectors. It is thoroughly explained here:
https://rsaxvc.net/blog/2014/2/1/Inside_the_EDUP_EP-AB003_24GHz_Amplifier.html

 A handful of modifications have been proposeed for the QO-100 scenario. Some operators find it helpful to put the amplifier in permanent Tx mode to prevent it from switching back and forth in SSB mode when the carrier power drops in the silent breaks; others find it useful to bridge the switches to avoid the marginal loss of around 0.5 dB.
 
Links:

https://www.myadventuresinengineering.com/2020/01/my-adventures-in-satellite.html

https://www.george-smart.co.uk/2019/03/limesdr-edup-pa/

https://rfsparkling.com/blog/2019/08/30/modifying-edup-ep-ab007-4w-bi-directional-wifi-amplifier-for-oa-100-eshail-satellite-operation-non-stop-tx-mode/


I myself do not find these modifications very rewarding because they are messy, the power output increase is marginal and if Tx input power is set to 100 mW full scale, the Tx should not drop out in the silent breaks anyway. So i will concentrate on adding a distinct port that connects to the Rx input of the SDR.

![P4022700](https://user-images.githubusercontent.com/96028811/231276899-d7849688-a6c9-4a83-8800-4f614ad572c6.JPG)

For this simply a pigtail is added at the end of the Rx path:

![P4022704](https://user-images.githubusercontent.com/96028811/231277107-f3b4ecd0-afe9-4419-88d8-c29470e04dea.JPG)

![P4102715](https://user-images.githubusercontent.com/96028811/231277157-b37cafb6-b07a-4fc9-aeca-1f7e48016013.jpg)


The main concern is whether during Tx, the crosstalk into the newly added Rx port is small enough to not fry the input stage of our SDR; therefore an isolation damping measurement shall be performed. The input is a PLL synthesizer followed by two SPF5189z LNAs in series, to pre-amplify the 1 mW signal to the required 100 mW. At the output and the Rx input we connect power meters.

![isolation_damp_setup](https://user-images.githubusercontent.com/96028811/231277258-b1d405d1-59f2-43fb-ad53-2e3216d15fbe.jpg)

When hitting the input with the 100 mW required to get the 4W output power, we meausure 35.7 dBm at the antenna port and -2.5 dBm at the Rx port.  According to the datasheet of the AD9363 (https://www.analog.com/media/en/technical-documentation/data-sheets/AD9363.pdf table 10) , the Adalm Pluto has a maximum tolerable input power of +2.5 dBm. So it should be safe to hook it up to the Pluto SDR, provided that the booster switches fast enough (typically: a microsecond, for compatibility with WLAN) so that the little burst power before switching to Tx mode is brief enough to not cause any damage to it. Enjoy at your own risk ! As an additional protection, an attenuator can be inserted into the receive path. According to the seller specifications, the receive path of the EP-AB003 booster has 10..12 dB gain, and even 25 dB is stated in the attached manual. Therefore, inserting e.g. a 6 dB attenuator should still maintain the gain positive. With devices that have a combined Rx/Tx port, the modified WLAN booster should work as before since we have not modified anything else. The switching threshold might be slightly increased. I recommend terminating the unused Rx port with a dummy load, to avoid that the open circuit SMA connector translates into a short at the soldering point.

![P4102728](https://user-images.githubusercontent.com/96028811/231277491-6d50c383-fec2-4d53-bca8-16491237cb84.jpg)

Alternatively, the length of the pigtail may be optimized to n times half wavelength so that an open circuit on the SMA connector transforms also into a open circuit at the soldering point.

Note that the receive path of the WLAN booster is likely to have better characteristics in terms of noise figure than for example a cheap WiFi module, and also features a SAW filter, so it not only boosts Tx power but also improves reception. Otherwise it would be useless in extending WLAN range, if used only on one side of the communication link.


The cleanup

In the end, everything is nicely put into a aluminum box.

![mainframe front](https://github.com/technologiesubtile/RxTx_WLAN_booster/assets/96028811/2da7e2c0-3eb6-4cb4-9234-6f7f80f4469c)
It contains power supply, bias T, the modified amplifier and a DC-DC converter that makes 5 V out of 12 V for the LNA preamps and additional accessories.

![mainframe right](https://github.com/technologiesubtile/RxTx_WLAN_booster/assets/96028811/6b34a360-01f2-4e06-bd81-0791f265defe)
The reason to step down from 12 V is the possibility to power everything from a car battery. Note that the converter is a dismantlede cigarette lighter to USB adapter that readily comes with the USB outlets.

![mainframe_front_upper](https://github.com/technologiesubtile/RxTx_WLAN_booster/assets/96028811/ae275d16-ae30-4a8d-b799-7291349bf82b)
An intermediate shelf plate can be inserted and the upper floor has place to hold a SDR, WLAN Router, Bluetooth Adapter or as shown here a PLL synthesizer used for testing the satellite uplink with a CW signal.

A typical use scenario of the modified booster would be half duplex terrestrial two-way communication, using a satellite dish antenna provided with the patch or helical feeder for 2.4 GHz as for QO-100 operation. In this case the LNB sould be unused and RX and Tx be done via the 2.4 GHz patch antenna. Also the dish would then be aiming slightly downward to obtain horizontal path as for terrestrial links, or turned upside down with the feeder arm on top.

![WP_20230304_15_47_10_Pro](https://user-images.githubusercontent.com/96028811/231277669-237445d3-3e47-4b5e-8d8a-8af53bd31fcc.jpg)

Similar mod by VK2XAX

It seems that a similar modification had been proposed by OM VK2XAX but i cannot find the original work. According to the discussion on the Amsat forum, 

https://forum.amsat-dl.org/index.php?thread/242-tx-only-modification-on-commercial-wifi-booster/&pageNo=2

he had replaced the input switch by 2 sma connectors right away. My modification is less invasive by leaving the existing connector and Rx/Tx auto switched port, whereas the newly added port is Rx only.


Amplifier for 1296 MHz with a similar connection scheme

After sharing my modification online, i discovered, on the youtube channel Ham Radio DX, an amplifier by SG Labs for the 1296 MHz ham band that seems to have the same port pattern as my EP-AB003 amplifier after modification. The newly added port corresponds to the RX(Opt) port of the SG Labs amplifier. Link: https://www.youtube.com/watch?v=5w8sGDdzz2I

![sglabs1296diagram](https://user-images.githubusercontent.com/96028811/233620261-95f4f8d6-a22b-4830-b803-237896f96946.jpg)


Further ideas and perspectives

The receive path of our amplifier features a SAW filter that can be expected to be quite efficient in suppressing harmonics or other spurious components of the spectrum. If the amp is used for QO-100 and the receive path is not used as such, an idea would be to use it in the preamplification of the TX path to remove the harmonics of the SDR output. However, doing so requires measuring some characteristics that are not documented. The amplification is given as 10..12 dB (seller) or 15 dB (manual) but nothing is known about third order intercept. The noise figure is stated on the manual as 2.5 dB which might be slightly better than the one of a cheap WiFi module that i would guess to be 3..4 dB. If this receive path can be driven to output 0 dBm, it could be hooked up between the Pluto output, preceded by a 10 dB attenuator, and followed by the 2 SPF 5189Z. It is certainly not designed to output more, since at 3 dBm it would exceed the switching threshold of the carrier detection and commute the booster into Tx mode. However, it would also be interesting how much harmonics are generated by the finals, and to what extent they are attenuated by the impedance transformer that seems to be a slug to adapt the low impedance output of the transistors to the 50 Ohm line. Therefore additional measurements need to be performed with a spectrum analyzer to evaluate the overall performance of such a configuration.


Miscellaneous

For soldering, a small soldering iron (18 W) is suitable for soldering the core of the coax to the signal path of the GSG line; however, for soldering the shielding onto the ground plane, the small soldering iron revealed to be not enough powerful to melt the solder on the thermally conducting copper plane and a 100 W soldering gun was used instead. The coax pigtail soldered to the GSG lane cane easily cause a short circuit when the shielding touches the signal path. To verify, i recommend measuring the DC resistance on the SMA connector which should be approximately 92 Ohm. On some boosters it is hard to remove the back cover. In this case, i recommend, after removing the allen screws, to force a 6 mm screw some gears into one of the holes to use it as a handle for pulling out the cover plate.


Disclaimer

DIY project - no liability is assumed, no guarantee of any kind is given. Make sure to have the necessary skills, training, and tools. Radiotransmitting device: it is the users sole responsibility to comply with local regulations, including maximum authorized EIRP, suppression of spurious transmissions and harmonics, and to hold the required permissions, e.g. amateur radio certificate for applications other than ISM.
