+++
showonlyimage = true
draft = false
image = "img/portfolio/W8/w8000.webp"
date = "2023-07-12T15:34:22+02:00"
title = "Chocolate Survey Terminal"
weight = 5
+++

![Introduction][1]  
This EDA was inspired by a ***Tidy Tuesday*** dataset that looked at the ratings of different cacao been suppliers in different countries. Rather than following the original data challenge, I decided to instead focus on the elements that dealt with South Africa (SA) because my first real look at the data showed that SA had received the lowest ratings.

The hypothetical challenge I set for myself was to use the data to identify what led to the poor ratings as well as what changes would need to be made to improve future results.

The project is divided into two parts:  
  - **Part 1** is an exploratory data analysis of the data   
  - **Part 2** is the creation of an electronic device that could be used to test the results  
  
The the exploratory segment I went into this exercise with a specific set of questions that were going to impact what I looked for and what I wouldn't look too deep into. The questions are:  
  - Where did South Africa rank overall?
  - What company produces the most popular bean?
  - What is the most popular ingredient combination?

The answers from the questions above will help make an informed recommendation for some changes that a potential client might be looking to make to improve the performance of their chocolate product.  

I also didn't rule out new questions being introduced where they could make certain points clearer.  
  
![Step 1: First Look][2]  

The methodology used is outlined in [R Programming 101's](https://youtube.com/@RProgramming101/) series of tutorials. There were a number of other creators such as [Statistics Globe](https://youtube.com/@StatisticsGlobe) and [Data Slice](https://youtube.com/@dataslice) that I refer back to often when working on EDA's. 

The first step is loading all the packages to be used:  

```{r}
library(tidyverse)
library(dplyr)
library(ggplot2)
library(gganimate)
library(ggthemes)
library(gifski)
```  
I brought the dataset into the ***Environment*** using the chunk above, then used ***glimpse()*** for a quick inspection.  

```{r}  
chocolate <- read.csv("/home/afrikaniz3d/choglud/0_tidy_data/chocolate.txt")
```


```{r}  
> glimpse(chocolate)
Rows: 2,530
Columns: 10
$ ref                              <int> 2454, 2458, 2454, 2542, 2546, 2546, 2542, 797, 797, 1011, 1015…
$ company_manufacturer             <chr> "5150", "5150", "5150", "5150", "5150", "5150", "5150", "A. Mo…
$ company_location                 <chr> "U.S.A.", "U.S.A.", "U.S.A.", "U.S.A.", "U.S.A.", "U.S.A.", "U…
$ review_date                      <int> 2019, 2019, 2019, 2021, 2021, 2021, 2021, 2012, 2012, 2013, 20…
$ country_of_bean_origin           <chr> "Tanzania", "Dominican Republic", "Madagascar", "Fiji", "Venez…
$ specific_bean_origin_or_bar_name <chr> "Kokoa Kamili, batch 1", "Zorzal, batch 1", "Bejofo Estate, ba…
$ cocoa_percent                    <chr> "76%", "76%", "76%", "68%", "72%", "80%", "68%", "70%", "63%",…
$ ingredients                      <chr> "3- B,S,C", "3- B,S,C", "3- B,S,C", "3- B,S,C", "3- B,S,C", "3…
$ most_memorable_characteristics   <chr> "rich cocoa, fatty, bready", "cocoa, vegetal, savory", "cocoa,…
$ rating                           <dbl> 3.25, 3.50, 3.75, 3.00, 3.00, 3.25, 3.50, 3.50, 3.75, 2.75, 2.…  
```  

![Step 2: Cleaning][3]

The next step was checking for duplicates or misspellings. I did this by using the ***unique()*** function in each column/variable.  

```{r}
> unique(chocolate$ref)
  [1] 2454 2458 2542 2546  797 1011 1015 1019 1315 1319 1676 1680 1704 1876 2206 2648 1462 1470 2462 2470  705 2438 2442  370
 [25]  316  502  508  636 1061 1173 1215 1992 1944 1125 1133 1129 1732 1728 2044  147  129  175  304  363  544  470  725  327
 [49]  464  322 1964 1145 1494 1498   75  123  170  979 2088 2092 2434  572 1065 1259 1852 2586 1379 1375 1602 1534 1598 1904
 [73] 1928 1724 1900 1908 1924 2020 2028 2024 2068 2254 2450 2330 2166 2162  300  355  486  600  531  745  729  947 1193 1181
 [97] 2562 2566  141  331  647  661 1780 2056 2672  999  995 1474 2146 1454 2290 2294  983 1295 1554 1980  955 1880 1840 1868
[121] 2374 1948 1784 1788  586 1804 1800 1864 2190 1768 2246  773  757 1141 1482 1486 2084  233  256  414  423  431  558  565
[145]  478  963 2108 2114 2422 2574 1331 2590 1046  911 1752 1756 1740 1996   81   24   32   48  199  336  395  761  629  672
[169] 1042 1038 1418 1339 1912 2250 2554  341 1267 1271 2194 2096 1255 1355 1984 1518 1514 1149 1235 1231 1638 2052 1323 1299
[193] 1303 2314 2274 2266 1606 1716 2226 2230 1391 1395 1668 1692 1688 2238 2278 2076 1434 1430 1662 1816 1860  641  991  346
[217]  537  523 2510 1684 2610 2614  259  245  237  296  749  765  809 1185 1442 1808 2558  266  269 1263 2626 2630  845  841
[241] 1586 1582 1884 1888 2154 2394 2402 1105 1153 1101 1109 1407 1347 1371 1570 1574 1956 2354 2688 1247 1251  404 1426 2676
[265]  252 2202 2668 1696 2298 1772 1760 1764 1530  919 1121 2700 2134 1219 2080  682 1832 2594 2598  117  209  439 1522  377
[289] 2534 2514 2638 2310 2306  666  445  227 2474 2606  292  579 1630 1916 2684  196  220  451  817  813  959  821 1169  971
[313]  975 1283 1335 1736 2466 2502 2656 2526 2522 2622  785 1654 1666 1610 2064  805  915 1026 1085 1387 1446 2178 2386 2602
[337] 2550  654  987 1223 1920 2342 1550 1708  907 1093  180  263 2222  166  241  769  895 1034 1634 1952 1972 2016 2258 2270
[361] 2406  311   63   87  135  192  111  272  693  863 1672 2040 2696 2390  516  697  623  923 2100 2366 1506 1626 1343 1812
[385] 2358  709  206 2382 1137 1450 2118 2198 2350 1209 1205  903 2570 1275 2142 2664  105   56  494 1287 2482 2478 2486 2398
[409] 2150 1776 2072 2430 2712 2318 2322 2326 2122 1351 1542 1538  781  887  883 1940  688  642  931  899  927 1030 1081 1117
[433] 1383 2174 2708 2642 2644 2704 2000 2126 2130  753 1359 1367 1642 1646 2012 2282 1848 2032 2446  741 2692   15 1896 2060
[457]   99   93  276  387  552 1053 1197 1201 1327  701 1700 2378 2302 1748 1243 1239 1744 1458 1546 1712 2426 1466 1872 1113
[481] 1097 1307 1311  737  733 2008  943 1279    5 1976 2104 2218 2214 1069 1073 2498 2338 2334  837  248 1968 2286 1422 1363
[505]  597  576  891 1892 2138  230 1189  721  713 2186 2182 1438 1590 1594  833  853 2048 2262 1566 1562 1856  855  157 2538
[529] 1049 1502 2518 2506  284  288  607 1089  308  184 1007 1003  871  875 1844 1478 2158  849 1650 1836  777 1177 1291 2362
[553] 2170 2210 1490 2112  825 2346 1960 2370 2652 1618 1614 2680 1658 2490  867 1399 2418 1403 1526 1022  935 1558 1411 2494
[577] 2582  457 2036 1161 1165  859  224 1415 2234 1720 1510 2530  829  789 1820   40  162  280  202  717 2410 2414 1227 1988
[601] 1057 1796 2618  967  213  793 2004 2660  615  188 1622 1792 1157  676  951 1077 1828 2242 1932 1936  939 2634  801  153
[625] 1211 2578  593 1578 1824  879
``` 

There are 630 recorded reference numbers. This is the number I'll be looking for when going through the next variable - "company_manufacturer"

```{r}
> unique(chocolate$company_manufacturer)
  [1] "5150"                                    "A. Morin"                               
  [3] "Acalli"                                  "Adi aka Fijiana (Easy In Ltd)"          
  [5] "Aelan"                                   "Aequare (Gianduja)"                     
  [7] "Ah Cacao"                                "Akesson's (Pralus)"                     
  [9] "Alain Ducasse"                           "Alexandre"                              
 [11] "Altus aka Cao Artisan"                   "Amano"                                  
 [13] "Amatller (Simon Coll)"                   "Amazing Cacao"                          
 [15] "Amazona"                                 "Ambrosia"                               
 [17] "Amedei"                                  "AMMA"                                   
 [19] "Anahata"                                 "Animas"                                 
 [21] "Ara"                                     "Arete"                                  
 [23] "Argencove"                               "Artisan du Chocolat"                    
 [25] "Artisan du Chocolat (Casa Luker)"        "Aruntam"                                
 [27] "Askinosie"                               "Atypic"                                 
 [29] "Auro"                                    "Avanaa"                                 
 [31] "Bahen & Co."                             "Baiani"                                 
 [33] "Bakau"                                   "Bankston"                               
 [35] "Bar Au Chocolat"                         "Baravelli's"                            
 [37] "Batch"                                   "Bean"                                   
 [39] "Beau Cacao"                              "Beehive"                                
 [41] "Belcolade"                               "Bellflower"                             
 [43] "Belvie"                                  "Belyzium"                               
 [45] "Benns"                                   "Benoit Nihant"                          
 [47] "Bernachon"                               "Beschle (Felchlin)"                     
 [49] "Bisou"                                   "Bitacora"                               
 [51] "Bittersweet Origins"                     "Bixby"                                  
 [53] "Black Mountain"                          "Black River (A. Morin)"                 
 [55] "Black Sheep"                             "Blanxart"                               
 [57] "Blue Bandana"                            "Boho"                                   
 [59] "Bonaterra"                               "Bonnat"                                 
 [61] "Bouga Cacao (Tulicorp)"                  "Bowler Man"                             
 [63] "Brasstown"                               "Brasstown aka It's Chocolate"           
 [65] "Brazen"                                  "Breeze Mill"                            
 [67] "Bright"                                  "Britarev"                               
 [69] "Bronx Grrl Chocolate"                    "Bullion"                                
 [71] "Burnt Fork Bend"                         "By Cacao"                               
 [73] "Cacai Cacao"                             "Cacao 70"                               
 [75] "Cacao Arabuco"                           "Cacao Atlanta"                          
 [77] "Cacao Barry"                             "Cacao Betulia"                          
 [79] "Cacao de Origen"                         "Cacao Gonzales"                         
 [81] "Cacao Hunters"                           "Cacao Market"                           
 [83] "Cacao Prieto"                            "Cacao Sampaka"                          
 [85] "Cacao Santa Fe (Art of Chocolate)"       "Cacao Store"                            
 [87] "Cacaodada"                               "Cacaosuyo"                              
 [89] "Cacaoyere (Ecuatoriana)"                 "Callebaut"                              
 [91] "C-Amaro"                                 "Cao"                                    
 [93] "Caofiori"                                "Caoni (Tulicorp)"                       
 [95] "Captain Pembleton"                       "CAR Artisan"                            
 [97] "Caribeans"                               "Carlotta Chocolat"                      
 [99] "Casa"                                    "Casa Lasevicius"                        
[101] "Castronovo"                              "Cello"                                  
[103] "Cemoi"                                   "Chaleur B"                              
[105] "Chapon"                                  "Charm School"                           
[107] "Chchukululu (Tulicorp)"                  "Chequessett"                            
[109] "Chloe Chocolat"                          "Chocablog"                              
[111] "Choco Del Sol"                           "Choco Dong"                             
[113] "Chococard (Lapos)"                       "Chocolarder"                            
[115] "Chocola'te"                              "Chocolate Alchemist-Philly"             
[117] "Chocolate Con Amor"                      "Chocolate Conspiracy"                   
[119] "Chocolate Makers"                        "Chocolate Tree"                         
[121] "Chocolates by Josh (Box Chocolate)"      "Chocolatoa"                             
[123] "Chocolats Privilege"                     "Chocolibrium"                           
[125] "ChocoReko"                               "Chocosol"                               
[127] "Chocotenango"                            "Chocovic"                               
[129] "Chocovivo"                               "Choklat"                                
[131] "Chokola"                                 "Chokolat Elot (Girard)"                 
[133] "Christophe Toury"                        "Christopher Elbow"                      
[135] "Christopher Morel (Felchlin)"            "Chuao Chocolatier"                      
[137] "Chuao Chocolatier (Pralus)"              "Claudio Corallo"                        
[139] "Cleveland Chocolate Company"             "Cloudforest"                            
[141] "Coco"                                    "Cocoa Carib"                            
[143] "Cocoa Forge"                             "Compania de Chocolate (Salgado)"        
[145] "Condor"                                  "Confluence"                             
[147] "Conjure"                                 "Coppeneur"                              
[149] "Cote d' Or (Kraft)"                      "Cravve"                                 
[151] "Creo"                                    "Crow and Moss"                          
[153] "Cultura"                                 "Cuna de Piedra"                         
[155] "Daintree"                                "Dalloway"                               
[157] "Damson"                                  "Dancing Lion"                           
[159] "Dandelion"                               "Dandelion (aka Brower Ave)"             
[161] "Dandelion (Japan)"                       "Danta"                                  
[163] "DAR"                                     "Darcis"                                 
[165] "Dark Forest"                             "Davis"                                  
[167] "De Mendes"                               "De Villiers"                            
[169] "Dean and Deluca (Belcolade)"             "Debauve & Gallais (Michel Cluizel)"     
[171] "Definite"                                "Desbarres"                              
[173] "DeVries"                                 "Dick Taylor"                            
[175] "Diogo Vaz"                               "Doble & Bignall"                        
[177] "Dole (Guittard)"                         "Dolfin (Belcolade)"                     
[179] "Domori"                                  "Dormouse"                               
[181] "Double Spiral"                           "Duffy's"                                
[183] "Dulcinea"                                "Durand"                                 
[185] "Durci"                                   "East Van Roasters"                      
[187] "Eau de Rose"                             "Eclat (Felchlin)"                       
[189] "Eclat (Fruition)"                        "Edelmond"                               
[191] "El Buen"                                 "El Ceibo"                               
[193] "El Rey"                                  "Eldora"                                 
[195] "Emerald Estate"                          "Emily's"                                
[197] "Encuentro"                               "ENNA"                                   
[199] "Enric Rovira (Claudio Corallo)"          "Erithaj (A. Morin)"                     
[201] "Escazu"                                  "Ethel's Artisan (Mars)"                 
[203] "Ethereal"                                "Exquisito"                              
[205] "Fearless (AMMA)"                         "Feitoria Cacao"                         
[207] "Felchlin"                                "Finca"                                  
[209] "Finnia"                                  "Firetree"                               
[211] "Five (5)Mile"                            "FJAK"                                   
[213] "Forever Cacao"                           "Forteza (Cortes)"                       
[215] "Fossa"                                   "Foundry"                                
[217] "Franceschi"                              "Frederic Blondeel"                      
[219] "French Broad"                            "Fresco"                                 
[221] "Fresh Coast"                             "Fresh Coast aka Just Good Choc."        
[223] "Friis Holm"                              "Friis Holm (Bonnat)"                    
[225] "Fruition"                                "Fu Wan"                                 
[227] "Garden Island"                           "Georgia Ramon"                          
[229] "Glennmade"                               "Goodnow Farms"                          
[231] "Gotham"                                  "Grand Place"                            
[233] "Great Lakes"                             "Green & Black's (ICAM)"                 
[235] "Green Bean to Bar"                       "Green Door"                             
[237] "Grenada Chocolate Co."                   "Guido Castagna"                         
[239] "Guittard"                                "Habitual"                               
[241] "Hachez"                                  "Hacienda El Castillo"                   
[243] "Haigh"                                   "Harper Macaw"                           
[245] "Harris & James"                          "Hazel Hill"                             
[247] "Hecho"                                   "Heilemann"                              
[249] "Heinde & Verre"                          "Heirloom Cacao Preservation (Brasstown)"
[251] "Heirloom Cacao Preservation (Fruition)"  "Heirloom Cacao Preservation (Guittard)" 
[253] "Heirloom Cacao Preservation (Manoa)"     "Heirloom Cacao Preservation (Millcreek)"
[255] "Heirloom Cacao Preservation (Mindo)"     "Heirloom Cacao Preservation (Zokoko)"   
[257] "hello cocoa (now Markham & Fitz)"        "Hemisphere"                             
[259] "hexx"                                    "Hogarth"                                
[261] "Hoja Verde (Tulicorp)"                   "Holy Cacao"                             
[263] "Honest"                                  "Hotel Chocolat"                         
[265] "Hotel Chocolat (Coppeneur)"              "Hummingbird"                            
[267] "Idilio (Felchlin)"                       "Indah"                                  
[269] "Indi"                                    "iQ Chocolate"                           
[271] "Isidro"                                  "Islands Chocolate"                      
[273] "Izard"                                   "Jacque Torres"                          
[275] "Jean Marie Auboine"                      "Johnny Iuzzini"                         
[277] "Jordis"                                  "Kad Kokoa"                              
[279] "Kah Kow"                                 "Kah Kow - USA"                          
[281] "Kaitxo"                                  "Kakao"                                  
[283] "Kallari (Ecuatoriana)"                   "Kaoka (Cemoi)"                          
[285] "Kasama"                                  "Kerchner"                               
[287] "Ki' Xocolatl"                            "Kin + Pod"                              
[289] "Kiskadee"                                "Krak"                                   
[291] "Kto"                                     "K'ul"                                   
[293] "Kyya"                                    "L.A. Burdick (Felchlin)"                
[295] "La Cascade du Chocolat"                  "La Chocolaterie Nanairo"                
[297] "La Chorena"                              "La Feverie (Hasnaa)"                    
[299] "La Maison du Chocolat (Valrhona)"        "La Oroquidea"                           
[301] "La Pepa de Oro"                          "La Rifa"                                
[303] "Laia aka Chat-Noir"                      "Lajedo do Ouro"                         
[305] "Lake Champlain (Callebaut)"              "L'Amourette"                            
[307] "Land"                                    "Landmark (Amano)"                       
[309] "Legast"                                  "Letterpress"                            
[311] "Levy"                                    "Lilla"                                  
[313] "Lillie Belle"                            "Lindt & Sprungli"                       
[315] "Lirio"                                   "Loiza"                                  
[317] "Lonohana"                                "Loon"                                   
[319] "Love Bar"                                "Love Brown"                             
[321] "Luisa Abram"                             "Luisa's Vegan"                          
[323] "Luker"                                   "Lumineux"                               
[325] "Machu Picchu Trading Co."                "MaDe Atlantic City Chocolate Bar"       
[327] "Madecasse (Cinagra)"                     "Madhu"                                  
[329] "Madre"                                   "Maglio"                                 
[331] "Majani"                                  "Malagasy (Chocolaterie Robert)"         
[333] "Malagos"                                 "Malai"                                  
[335] "Malie Kai (Guittard)"                    "Malmo"                                  
[337] "Mana"                                    "Manifesto Cacao"                        
[339] "Manoa"                                   "Manufaktura Czekolady"                  
[341] "Map Chocolate"                           "Marana"                                 
[343] "Maribea"                                 "Marigold's Finest"                      
[345] "Markham & Fitz"                          "Marou"                                  
[347] "Mars"                                    "Marsatta"                               
[349] "Martin Mayer"                            "Mast Brothers"                          
[351] "Matale"                                  "Maui Kuia"                              
[353] "Maverick"                                "Mayacama"                               
[355] "McGuire"                                 "Meadowlands"                            
[357] "Mellow"                                  "Menakao (aka Cinagra)"                  
[359] "Mesocacao"                               "Mestico"                                
[361] "Metiisto"                                "Metropolitan"                           
[363] "Meybol"                                  "Michel Cluizel"                         
[365] "Middlebury"                              "Mike & Becky"                           
[367] "Millcreek Cacao Roasters"                "Millesime"                              
[369] "Milton"                                  "Mindo"                                  
[371] "Minimal"                                 "Mirzam"                                 
[373] "Misina"                                  "Mission"                                
[375] "Mita"                                    "Moho"                                   
[377] "Moka Origins"                            "Moku"                                   
[379] "Molucca"                                 "Momotombo"                              
[381] "Monarque"                                "Monsieur Truffe"                        
[383] "Monsoon"                                 "Montecristi"                            
[385] "MUCHO"                                   "Muchomas (Mesocacao)"                   
[387] "Musee du Chocolat Theobroma"             "Mutari"                                 
[389] "Nahua"                                   "Naive"                                  
[391] "Nanea"                                   "Nathan Miller"                          
[393] "Nearynogs"                               "Neuhaus (Callebaut)"                    
[395] "Nibble"                                  "Night Owl"                              
[397] "Nikoa"                                   "Nina"                                   
[399] "Ninth (9th) & Larkin"                    "Noble Bean aka Jerjobo"                 
[401] "Noir d' Ebine"                           "Nova Monda"                             
[403] "Nuance"                                  "Nugali"                                 
[405] "Oakland Chocolate Co."                   "Obolo"                                  
[407] "Ocelot"                                  "Ocho"                                   
[409] "Odyssey"                                 "Ohiyo"                                  
[411] "Oialla by Bojessen (Malmo)"              "Olive and Sinclair"                     
[413] "Olivia"                                  "Omanhene"                               
[415] "Omnom"                                   "Or Dubh"                                
[417] "Orfeve"                                  "organicfair"                            
[419] "Original Beans (Felchlin)"               "Original Hawaiin Chocolate Factory"     
[421] "Orquidea"                                "Pacari"                                 
[423] "Palet D'Or"                              "Palette de Bine"                        
[425] "Pangea"                                  "Park 75"                                
[427] "Parliament"                              "Parre Chocolat"                         
[429] "Pascha"                                  "Patric"                                 
[431] "Paul Young"                              "Peppalo"                                
[433] "Perrenial"                               "Petite Patrie"                          
[435] "Pierre Marcolini"                        "Piety and Desire"                       
[437] "Pinellas"                                "Pitch Dark"                             
[439] "Pollinator"                              "Pomm (aka Dead Dog)"                    
[441] "Poppy and Peep"                          "Potomac"                                
[443] "Pralus"                                  "Primo Botanica"                         
[445] "Public Chocolatory"                      "Pump Street Bakery"                     
[447] "Pura Delizia"                            "Q Chocolate"                            
[449] "Qantu"                                   "Quetzalli (Wolter)"                     
[451] "Raaka"                                   "Rain Republic"                          
[453] "Rancho San Jacinto"                      "Ranger"                                 
[455] "Raoul Boulanger"                         "Raphio"                                 
[457] "Raw Cocoa"                               "Republica del Cacao (aka Confecta)"     
[459] "Ritual"                                  "River-Sea"                              
[461] "Roasting Masters"                        "Robert (aka Chocolaterie Robert)"       
[463] "Rococo (Grenada Chocolate Co.)"          "Rogue"                                  
[465] "Rozsavolgyi"                             "Ruket"                                  
[467] "S.A.I.D."                                "Sacred"                                 
[469] "Salgado"                                 "San Jose"                               
[471] "Santander (Compania Nacional)"           "Santome"                                
[473] "Scharffen Berger"                        "Seaforth"                               
[475] "Seahorse"                                "Shane Chocolate Works"                  
[477] "Shark Mountain"                          "Shark's"                                
[479] "Shattell"                                "Sibu"                                   
[481] "Sibu Sura"                               "Silvio Bessone"                         
[483] "Sirene"                                  "Sjolinds"                               
[485] "Smooth Chocolator, The"                  "Snake & Butterfly"                      
[487] "Soeka"                                   "Soklet"                                 
[489] "Sol Cacao"                               "Solkiki"                                
[491] "Solomons Gold"                           "Solstice"                               
[493] "Soma"                                    "Somerville"                             
[495] "Soul"                                    "Spagnvola"                              
[497] "Spencer"                                 "Spinnaker"                              
[499] "Sprungli (Felchlin)"                     "SRSLY"                                  
[501] "Starchild"                               "Stella (aka Bernrain)"                  
[503] "Stone Grindz"                            "StRita Supreme"                         
[505] "Sublime Origins"                         "Summerbird"                             
[507] "Suruca Chocolate"                        "Svenska Kakaobolaget"                   
[509] "sweet beans"                             "Sweet Escalier"                         
[511] "Sweetness"                               "Szanto Tibor"                           
[513] "Tabal"                                   "Tablette (aka Vanillabeans)"            
[515] "Tala"                                    "Tan Ban Skrati"                         
[517] "Taste Artisan"                           "Taste Artisan aka Coleman and Davis"    
[519] "Taucherli"                               "Taylor Made"                            
[521] "Taza"                                    "TCHO"                                   
[523] "Tejas"                                   "Terroir"                                
[525] "The Barn"                                "Theo"                                   
[527] "Theobroma"                               "Thistle & Rose aka Aggie USU"           
[529] "Tibito"                                  "Timo A. Meyer"                          
[531] "Tiny House"                              "To'ak"                                  
[533] "To'ak (Ecuatoriana)"                     "Tobago Estate (Pralus)"                 
[535] "Tocoti"                                  "Tosier"                                 
[537] "Tree to Bar"                             "Treehouse"                              
[539] "Treehouse aka Indaphoria"                "Triangle Roasters"                      
[541] "Tribar"                                  "Tribe"                                  
[543] "Tsara (Cinagra)"                         "twenty-four blackbirds"                 
[545] "Two Ravens"                              "Un Dimanche A Paris"                    
[547] "Uncouth"                                 "Undone"                                 
[549] "Upchurch"                                "Urzi"                                   
[551] "Utopick"                                 "Vaka"                                   
[553] "Valrhona"                                "Vanleer (Barry Callebaut)"              
[555] "Vao Vao (Chocolaterie Robert)"           "Vicuna"                                 
[557] "Videri"                                  "Vietcacao (A. Morin)"                   
[559] "Vintage Plantations"                     "Vintage Plantations (Tulicorp)"         
[561] "Violet Sky"                              "Vivra"                                  
[563] "Wellington Chocolate Factory"            "White Label aka Mutari"                 
[565] "Whittakers"                              "Wilkie's Organic"                       
[567] "Willie's Cacao"                          "Wm"                                     
[569] "Woodblock"                               "Xocolat"                                
[571] "Xocolatisimo"                            ***"Xocolatl"***                               
[573] "Xocolla"                                 "Zac Squared"                            
[575] "Zacharias"                               "Zak's"                                  
[577] "Zart Pralinen"                           "Zokoko"                                 
[579] "Zoto (Chocolatoa)"                       "Zotter"                                 
> 
```
I took a look through the list, looking for cases where there were differences in spelling. I found that although this wasn't the case there were many instances where there were a handful of companies/manufacturers that operated under different names. I also had to check to see if there wasn't any further overlap using a third, or fourth name (there were).  

I did this part of the exercise manually on sheets of paper - I understand that there are functions to perform this, but it would have made this portion of the report (showing each step) so much longer. Here are snaps of the sheets I checked against:  

![Checking for duplicates or alternates][4]  

![Step 3: Top 10][5]  

Before transforming the data I first wanted to see what the Top 10 ranking was as-is. This was done by creating a new data frame that was ordered (descending) by the rating value using the chunk below:  

```{r}
chocolate_ranked <- chocolate[order(chocolate$rating, decreasing = TRUE),]
```  
To make it a "Top 10" data frame I use the line below:  

```{r}
chocolate_top10 <- head(chocolate_ranked, 10)
```
The results show that A. Morin occupied the top spots.

```{r}
> chocolate_top10
     ref company_manufacturer company_location review_date country_of_bean_origin
19  1015             A. Morin           France        2013              Venezuela
20  1019             A. Morin           France        2013                   Peru
25  1319             A. Morin           France        2014                   Peru
33  2648             A. Morin           France        2021                 Mexico
80   470                Amano           U.S.A.        2010                Ecuador
81   725                Amano           U.S.A.        2011       Papua New Guinea
112  572                 AMMA           Brazil        2010                 Brazil
130 1598                Arete           U.S.A.        2015              Nicaragua
142 1908                Arete           U.S.A.        2016             Costa Rica
143 1924                Arete           U.S.A.        2016                   Peru
     specific_bean_origin_or_bar_name cocoa_percent ingredients most_memorable_characteristics rating
19                              Chuao           70%  4- B,S,C,L  oily, nut, caramel, raspberry      4
20               Chanchamayo Province           63%    3- B,S,C        sweet, cocoa, tangerine      4
25                            Pablino           70%  4- B,S,C,L    delicate, hazelnut, brownie      4
33                            La Joya           70%  4- B,S,C,L     light color, fruit, yogurt      4
80                             Guayas           70%  4- B,S,C,V   strong spice, intense pepper      4
81                             Morobe           70%  4- B,S,C,V             tart, lemon, smoke      4
112 Monte Alegre, 3 diff. plantations           60%  4- B,S,C,L     creamy, sweet,cocoa,banana      4
130                             Chuno           70%      2- B,S   creamy,sticky, peanut butter      4
142             Coto Brus, Terciopelo           70%      2- B,S        balanced, cherry, choco      4
143                           Phantom           70%      2- B,S      creamy, complex, balanced      4
```  

During a short break I realised that finding the "Top" 10 results is not as simple a task when you consider that the surveys are not of the same control group. The results specific to South Africa were also not helpful due to low volume (only four entries). My suggestion is then to look past establishing what the "African Chocolate Palette" is and focus on securing an African supplier of cacao instead. The argument can be this can be used to strengthen ties with a fellow African Union (AU) member.  

Now that I know what qualities are highly favoured in the overall Top 10 of the ratings, I can can compare this against the AU countries and choose five (5) choices that can be approached.  

Will the top African characteristics also be listed in the "sweet" and "fruity" region?  




![Step 4: African Grown][6]  

I used the chunk below to extract results where the beans were grown in Africa (or AU states), ranking them according to their survey rating:

```{r}
chocolate_africa_ranked <- chocolate_africa[order(chocolate_africa$rating, decreasing = TRUE),]
```  

```{r}
> head(chocolate_africa_ranked, 10)
     ref company_manufacturer company_location review_date country_of_bean_origin specific_bean_origin_or_bar_name cocoa_percent
39   629               Bonnat           France        2011             Madagascar         Madagascar, 100% criollo           75%
59  2514              Chokola           U.S.A.        2020             Madagascar        Bejofo, 2019 H., Batch 20           67%
88  2040               Domori            Italy        2018               Tanzania                         Tanzania           70%
110  915               Fresco           U.S.A.        2012             Madagascar   Sambirano Valley, #216, MR, LC           74%
121 1642        Georgia Ramon          Germany        2015                  Ghana                      ABOCFA Coop           70%
166  284  Madecasse (Cinagra)       Madagascar        2008             Madagascar                       Madagascar           63%
177 1177               Matale        Australia        2013             Madagascar                      Somia, 2013           68%
218  196               Patric           U.S.A.        2007             Madagascar                       Madagascar           70%
220  331               Patric           U.S.A.        2009             Madagascar                       Madagascar           75%
223  141     Pierre Marcolini          Belgium        2007             Madagascar   Sambirano, Ambanja, Madagascar           72%
     ingredients   most_memorable_characteristics rating
39      3- B,S,C       fatty, spicy, gentle roast      4
59        2- B,S cherry, perfectly balanced roast      4
88        2- B,S            creamy, sticky, fruit      4
110     3- B,S,C       caramel, nuts, dried fruit      4
121   4- B,S,C,L                cocoa and coconut      4
166 5- B,S,C,V,L            intense, nutty, cocoa      4
177     3- B,S,C                sticky, red fruit      4
218     3- B,S,C mild tart, wine, red fruit, long      4
220     3- B,S,C     simple red berry, rich, long      4
223 5- B,S,C,V,L      tangy, floral, spicy, cocoa      4  
```  

What I found is that ***Madagascar*** featured heavily throughout, especially in the top positions. The memorable characteristics are relatively similar to the French results.  

Madagascar also has the added benefit of being a close neighbour of Mzansi. Keeping it local to Madagascar with regards to the company to contact, the best (and only) option is [***Beyond Good***](https://beyondgood.com/) - formerly Madecasse(Cinagra).  

![Gif of Beyond Good Website][7]  

This concludes the data exploration.  

![Step 5: Designing the Survey Terminal][8]  

The 3D segment of this looks at creating a survey terminal. The idea is that these would be placed at popular chain stores and they would issue small samples for visitors to taste and comment on.  

![Refsheet for Survey Terminal][9]

Some design considerations I've come up with are:  
  - no buttons or touch screen - to minimise the need for cleaning
  - motion sensors to allow gesturing
  - video and audio recording for detailed comment/feedback  
  
I see it being a bougie vending machine.. 

TBC


[1]: /img/portfolio/W8/w8001.webp
[2]: /img/portfolio/W8/w8002.webp
[3]: /img/portfolio/W8/w8003.webp
[4]: /img/portfolio/W8/w8004.webp
[5]: /img/portfolio/W8/w8005.webp
[6]: /img/portfolio/W8/w8006.webp
[7]: /img/portfolio/W8/w8007.webp
[8]: /img/portfolio/W8/w8008.webp
[9]: /img/portfolio/W8/w8009.webp