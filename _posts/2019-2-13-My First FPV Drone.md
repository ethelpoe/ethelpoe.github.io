## Building my first FPV Drone

Material needed:

- x1 MPU6050 Sensor
- x1 NRF24L01 Transceiver
- x1 HC-6035 Mini Buzzer Pasivo
- x1 PFV camera [Link](https://www.amazon.com/gp/product/B06XJMQQ6Y?ie=UTF8&psc=1&linkCode=sl1&tag=maximaginatio-20&linkId=b023b3cabef5d05abcbbdf043b147588&language=en_US&ref_=as_li_ss_tl)

- x4 Coreless Motors (6mm) [Link](https://www.amazon.com/gp/product/B01N9ETA9Z?ie=UTF8&psc=1&linkCode=sl1&tag=maximaginatio-20&linkId=d6b1913793401950960ed46fa36f05a5&language=en_US&ref_=as_li_ss_tl) 

- x4 propellers [Link](https://www.amazon.com/Replacement-Propeller-CX-10A-CX-10C-Accessories/dp/B09BKR27XK?crid=1ZGGQSZXWS265&dib=eyJ2IjoiMSJ9.HJgRMGkstTO0HOPTyHmXXKjMIoZz-zjiWLLiM4r8HTrqLP4xwe5uoDylIQgmupLGv_Y0eJQH0azZ_5NrHQOVA3tnNy4yxlKZNnP5mtsM-5-lETHb7euhfjehcF6nZwYuswu-x5dlGWpsu2fwTA065w5vVD9TGI0Tdhq0akimfVMLD3pZegcGHXJpJG5ehKzjjqZ68pMPqPF3TIGTAf5bxMyv7RH2IMYr67Mzc8EQ7cm9dVHyYeLp4jz4eJ8Gf_YkFoK2GRYkax-feLCjWOWf80GnAuAQdiBiYqqCJNUdpS0.xwMjsE6cJdOMkA9EGygbOb95mH_k_fYVv17-pJI4AUo&dib_tag=se&keywords=30mm%2Bdrone%2Bpropeller&qid=1713616058&sprefix=30mm%2Bdrone%2Bpropeller%2Caps%2C169&sr=8-1&th=1&linkCode=sl1&tag=maximaginatio-20&linkId=e2817a000293c81f00acf6106713c401&language=en_US&ref_=as_li_ss_tl) [Link2](https://www.amazon.com/gp/product/B0768L5515?ie=UTF8&psc=1&linkCode=sl1&tag=maximaginatio-20&linkId=2145a1cbe30fda7f9c15f56c0c3a0057&language=en_US&ref_=as_li_ss_tl)

- x4 SMD MOSFET (Transistor SI2300) [Link](https://www.amazon.com/Todiys-SI2300DS-SI2300DS-T1-E3-N-Channel-Transistor/dp/B089KQPF1D?crid=YNCQ45YP7RBE&dib=eyJ2IjoiMSJ9.KbY11W99jYndcrYDuPAil_EFKJ9r7oG26pZJQ9Amb1GG7gcs1rkSzCDaGs3a4YpZJD_o-cOJMtikKtK6BHC4dP0mH9Xvgs77s0bilvAlrnjT8ZzTP1tQRxF20iZP_kND7UIGtRKOVJeBowxE-MkazeN5qnQXm_yAXKN7hofe2ypUFkstpExWes2XLqJ4JCiSImlhN59ifsKDL-Iyg9hpHoRxWfIm6ZaaBgYKZiJ-RF0.LU07JnF0c-wD14kN01KacK5b1iedovT2H-G9u7i8mQM&dib_tag=se&keywords=mosfet+si2300&qid=1713614768&sprefix=mosfet+si2300,aps,145&sr=8-3&linkCode=sl1&tag=maximaginatio-20&linkId=ec3c33efc6fef8f82876d8d432f06522&language=en_US&ref_=as_li_ss_tl) [Link2](https://www.amazon.com.mx/N-Channel-MOSFET-Transistor-SOT-23-CEGQOXSH/dp/B0D9RPFRHC?th=1)

- x4 SMD Resistor (10K) [Link](https://www.amazon.com/100-1206-Resistor-chip-0-25W/dp/B09ZJ6JGZ4) [Link2](https://www.amazon.com/dp/B08QRTQVP1?pd_rd_i=B08QRTQVP1&pd_rd_w=EBg5o&content-id=amzn1.sym.f734d1a2-0bf9-4a26-ad34-2e1b969a5a75&pf_rd_p=f734d1a2-0bf9-4a26-ad34-2e1b969a5a75&pf_rd_r=42GK53YT5RZVZZT7CN0F&pd_rd_wg=azWQv&pd_rd_r=ce820dd7-9c18-42c9-a182-f5358f64b126&s=industrial&sp_csd=d2lkZ2V0TmFtZT1zcF9kZXRhaWw&th=1&linkCode=sl1&tag=maximaginatio-20&linkId=177f4c503e74ecbe201dca1d33fbdc9c&language=en_US&ref_=as_li_ss_tl)

- x4 SMD Schottky Diode (SS14) [Link](https://www.amazon.com/Switching-Rectifier-15-Total-150pcs/dp/B07BTXK1NG?crid=1PN3ZWK34CD7A&dib=eyJ2IjoiMSJ9.aiBm8lDcPdJ97F0eiLwpoYc8GkbhF0jDPN2kjeOmVhyJzP18XKuME0fv0150FndocZyh8P0sGfMqG8ICvxW4p5_75FDJKNOwbxVyg0nJT_0zRQwY_4qDlykH7cImkbn_mcJCq_96Z_aAll-7hu2kVsjHVjbiYfvou0aKstWTXPrqfqaJkB7o97314apanmL22g-AcWBAMLZTcqR6Qr4kSbB4F7sJawqQ0QAWEk10SiZMUDrFIHpPnIqqqgv6ZjLNO3Xonbz0RfCqeXOmdKDLk7Ey61UkMs1gAqNhkp9pl6g.behOJIB9sVVF0dI57uZPIr5ffbJ5TrKOTvgoRunjNBY&dib_tag=se&keywords=0805+smd+rectifier+diode&qid=1713615046&s=industrial&sprefix=0805+smd+rectifiediode,industrial,123&sr=1-17-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9tdGY&psc=1&linkCode=sl1&tag=maximaginatio-20&linkId=1b8accb9b4ced5e08ebcc93ab880151c&language=en_US&ref_=as_li_ss_tl)

- x1 JST Connector

- x1 Lipo Battery (3.7V 220mAh) [Link](https://www.amazon.com/Lithium-Battery-Quadcopter-Helicopter-Accessories/dp/B08P74V3DP?crid=2L9JR5YTMM78L&dib=eyJ2IjoiMSJ9.wItSKZoMS9Biw2qPXDv3YBxXPqlxC8T3JDi1TV_OJBn6VPPYPZmRtNfH0fjrwAGZ9JCS81V1TvO-VmLZiNvSvMxgPz_ukpOA677lh42xYMRIiDfO77GrJk3f5Nrt2OkltlT5D8dIHn1ZJTwwgdoz_CIOfIxc8J0Grr6Z-iwS5RS17aV0BJ11ih6kfS-KWPZgiAxjiIoN4LgPS9uVPN3hwESQUvIcD2rae1Bye6jMJg8aeOWC7kjD3xqgrIhLXBtzWsU3W37lHxkSr4_cdLpP_G64Gksl8XFpc9mAAumY5Ag.QHpqqheEc_NrKRlnhembZDkHsSJcR3oddfL1Nr1myBs&dib_tag=se&keywords=250mah+lipo+battery&qid=1713614626&sprefix=250mah+lipo+batter,aps,121&sr=8-21-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9tdGY&psc=1&linkCode=sl1&tag=maximaginatio-20&linkId=6ec27565e0f44ef94b14cad7d4a6344b&language=en_US&ref_=as_li_ss_tl)

- Silicone wire (24AWG) [Link](https://www.amazon.com/gp/product/B01AAX64EC?ie=UTF8&th=1&linkCode=sl1&tag=maximaginatio-20&linkId=e6c149181f5da9453dda1a504e72cf56&language=en_US&ref_=as_li_ss_tl)

- Thin wires (30AWG) [Link](https://www.amazon.com/BNTECHGO-Silicone-Flexible-Resistant-Insulation/dp/B01M70EDCW?crid=3A3NTKHNTO96V&dib=eyJ2IjoiMSJ9.bfJRcye19aGiu_yRB0a4fajohApM5BQT_mHBz3c_2zZ-WweHgLLOfphSJ-XddL0-qOX5slmpuIzS-KxOG2iqi6-StXz5Mbx5zPSyz-uPSGvSQwb4ggWH-M_GSX4MehrJ5-bAC0Idfu12f6u_R4KqziZkUcUQvZ2tuXpxd4SbIwlbUCvqlQENzbwv-REs5EoyHwrvDLW2-oCYhdFRJYCgYysD8hXcfYYGU7xvBvgoB4YQfhQGTs-uxu7xvkpCs0JY6g453CXr25sZFxBgjYlrfXFtgIqLtlPjB44C6n9u1As.9cXHcNmgIJ8LWxrD4acVavI3b2JJ8JamkocyDsQmK70&dib_tag=se&keywords=32%2Bawg%2Bwire&qid=1713615570&s=hi&sprefix=32awg%2Bwir%2Ctools%2C120&sr=1-6&linkCode=sl1&tag=maximaginatio-20&linkId=cf9c5662d4803d7245622d76bfeffd53&language=en_US&ref_=as_li_ss_tl&th=1)

- x1 Perforated Prototyping Board [Link](https://www.amazon.com/ELEGOO-Prototype-Soldering-Compatible-Arduino/dp/B072Z7Y19F?crid=3CXZW17CXDD28&dib=eyJ2IjoiMSJ9.nnpDD3HaG1DRFqh-sUdRAz62HFqb0shq_re9UCjk4wjPYVn4Z8rb6Bl0LFk-4ShNEyeyOmXqrNPLUlJV2fJoabfSX-Q_cdtn_pH1CuKcYp9PSmiqb3d6W2zk7eAwJBKc7Cin2SZbgS2xU3XywrBwvnVj7jnuhMBZ3HS9k8G1AZJdOIfOJVRg4Ou_Uu94wV8LPQ9TmpORshaHOBxWXxcF9R909faQiacHK7x1A2O0pGk.31S4NPbuX4IznzXzzpXcMoa4CUxbiUkjqOippR36mEw&dib_tag=se&keywords=perforated+prototype+board&qid=1713615632&sprefix=perforated+prototype+boar,aps,127&sr=8-1&linkCode=sl1&tag=maximaginatio-20&linkId=783d8966ad01fea5a5446267a992547f&language=en_US&ref_=as_li_ss_tl)

- x1 Copper Sheet (30x18mm) [Link](https://www.amazon.com/uxcell-Flashing-Electricity-Projects-Multiple/dp/B0C5T6VVDM?crid=2KZW5UHM4K8V0&dib=eyJ2IjoiMSJ9.UZzTIEuh91cnQOvllaHoiVi6dBwoGlkYCgmuRML8Matvw2UhgLRihMBG33BBherGxKIxyGf4as35Q17OqHC1YIQZ-hCTDKPtnNpP6GprV-C4QpHsOkBQz3iN8ybYM9A1IRxjw0yJYrD2pwMyepMcLweYauPhVI0lSkObmoYjYMbWqu9oekYD-9HYtIl9s0gNRA8A-BdfNGpfCnPdoINEG6KURJmmiX3dnJowDtWzVJc.BHIQGc9Nlp6LekmYSmzRmMFPu6knSomlNhKIg5uUe8E&dib_tag=se&keywords=copper%2Bsheet&qid=1713615702&sprefix=copper%2Bsheet,aps,126&sr=8-3&th=1&linkCode=sl1&tag=maximaginatio-20&linkId=53d6126d5c09a7f8156346915dba1359&language=en_US&ref_=as_li_ss_tl) 

- Frame

## Other material used for this drone

- FPV Googles
- FTDI converter ( Mini USB a TTL adaptador)
- Pin Headers

---
### References

[Instructables](https://www.instructables.com/Make-a-Tiny-Arduino-Drone-With-FPV-Camera/)

[shop of items](https://iha-race.com/categoria-producto/microdones-fpv/)

[Beginner-Guide](https://rotorriot.com/pages/beginners-guide?srsltid=AfmBOopwwb7_azRZJLxc1t8_u6_mQJHUyT0ZpXETuMbdRQ4VV_zcc9Qs)

[Getting Starter With FPV drone](https://oscarliang.com/fpv-drone-guide/)

[Instructables Guide](https://www.instructables.com/Beginners-Guide-to-FPV-Drone-Racing/)

[Plan your Drone](https://vayuyaan.com/blog/diy-drone-exciting-guide-to-building-your-drone/)





