---
layout: default 
title: Federal PKI Trust Store Management Script
collection: tools
permalink: tools/
---
# Trust Store Management Script

The Trust Store Management Script (TSMS) was designed to help update operating system trust stores to facilitate smart card logon for PIV users. The script was designed to run using Python 3.x on Windows or macOS. 

The execution of the script will vary dependent upon operating system. 
- **On Windows** the script will assemble a .p7b file containing all operator desired PIV issuer certification authority (CA) signing certificates. 
- **On macOS** the script will assemble an Apple Configuration Profile (.mobileconfig) containing all operator desired PIV issuer certification authority (CA) signing certificates. 

Script output can be used to standardize and streamline existing agency trust store management processes. For example, on Windows, the resulting .p7b file could be published to domain controllers via certutil or a GPO. On macOS or iOS, the resulting .mobileconfig file could be shared on an agency intranet site or distributed to devices in one of the methods described [here](https://fpki.idmanagement.gov/truststores/apple/#installation-options/){:target="_blank"}. 


### Please see the sections below for more information on the script:
- [Download location](#download-location)
- [How it works](#how-it-works)
- [Target Certification Authorities](#target-certification-authorities)
- [System requirements](#system-requirements)
- [Running the script](#running-the-script)
- [Frequently asked questions](#faqs)

### Download Location
Version 1 of the Trust Store Management Script can be downloaded from this [link](TSMS-V1/Trust_Store_Mangagement_Script_V1.zip).

#### To ensure authenticity, please verify the package SHA-256 hash matches the below:
```
    EC2A7159E42CCA958190E50B18C6B35009234FD23B160731245239A09B36751A
```

#### Verifying hash on Windows using certutil...
```
    > certutil -hashfile [DOWNLOAD_LOCATION]\Trust_Store_Mangagement_Script_V1.zip SHA256
```

#### Verifying hash on macOS using Terminal…
```
    $ shasum -a 256 [DOWNLOAD_LOCATION]/Trust_Store_Mangagement_Script_V1.zip
```


### How it works

#### The TSMS installation package (Trust_Store_Mangagement_Script_V1.zip) contains three artifacts:
1. **certLoader.py** - This script reads in the targets.json installation file and assembles the output file containing the desired CA certificates.
1. **id-fpki-common-auth** - This directory contains CA certificates for the eligible installation targets. 
1. **targets.json** - This JSON file contains attributes for the potential CA installation targets. More information is presented below.


#### For each CA, the following attributes are included:
- **ID**: Contains a unique integer ID for the CA
- **SUBJECT**: Contains the CA’s subject name.
- **ISSUER**:  Contains the CA’s issuer.
- **VALIDFROM**:  Contains the validity start date for the certificate
- **VALIDTO**:  Contains the validity end date for the certificate
- **SERIAL**: Contains the certificate’s serial number
- **FILENAME**: Contains the certificate’s file name
- **THUMBPRINT**: Contains the certificate’s thumbprint (SHA-1 hash)
- **INSTALL**: Contains the installation flag (either TRUE or FALSE)


#### Sample below:
```
    "ID": "00001",
    "SUBJECT": "Betrusted Production SSP CA A1",
    "ISSUER": "Federal Common Policy CA",
    "VALIDFROM": "12/9/2010 19:55",
    "VALIDTO": "12/9/2020 19:49",
    "SERIAL": "19A",
    "FILENAME": "Betrusted_Production_SSP_CA_A1.cer",
    "THUMBPRINT": "0601bbdad5a28231bc9436750b4f3a484bab06c3",
    "INSTALL": "TRUE"
```

By default, all included CAs are marked for installation. Changing the installation flag from *TRUE* to *FALSE* will result in a given CA being omitted from the output file.

### Target Certification Authorities

The list of CAs included for installation in this script are presented below.

| ID    | Subject                                                  | Issuer                                | ValidFrom        | ValidTo          | SerialNumber                             |
|-------|----------------------------------------------------------|---------------------------------------|------------------|------------------|------------------------------------------|
| 00001 | Betrusted Production   SSP CA A1                         | Federal Common Policy   CA            | 12/9/2010 19:55  | 12/9/2020 19:49  | 19A                                      |
| 00002 | Bureau of the Census   Agency CA                         | Symantec SSP   Intermediate CA - G4   | 7/30/2015 0:00   | 11/11/2024 23:59 | 2355994850457C656B1B9A58E3FC3F98         |
| 00003 | Department of   Veterans Affairs CA                      | US Treasury Root CA                   | 8/28/2012 13:47  | 8/28/2022 14:17  | 4E397F22                                 |
| 00004 | Department of   Veterans Affairs CA                      | US Treasury Root CA                   | 10/17/2015 14:01 | 10/17/2025 14:31 | 4E398179                                 |
| 00005 | DHS CA4                                                  | US Treasury Root CA                   | 1/21/2011 19:11  | 1/21/2021 19:41  | 4A61D293                                 |
| 00006 | DHS CA4                                                  | US Treasury Root CA                   | 6/13/2015 14:35  | 6/13/2025 15:05  | 4E398128                                 |
| 00007 | DOD ID CA-41                                             | DoD Root CA 3                         | 11/9/2015 16:13  | 11/9/2021 16:13  | 18                                       |
| 00008 | DOD ID CA-42                                             | DoD Root CA 3                         | 11/9/2015 16:15  | 11/9/2021 16:15  | 19                                       |
| 00009 | DOD ID CA-43                                             | DoD Root CA 3                         | 11/9/2015 16:16  | 11/9/2021 16:16  | 1A                                       |
| 00010 | DOD ID CA-44                                             | DoD Root CA 3                         | 11/9/2015 16:18  | 11/9/2021 16:18  | 1B                                       |
| 00011 | DOD ID CA-49                                             | DoD Root CA 3                         | 11/22/2016 13:48 | 11/23/2022 13:48 | 127                                      |
| 00012 | DOD ID CA-50                                             | DoD Root CA 3                         | 11/22/2016 13:48 | 11/23/2022 13:48 | 128                                      |
| 00013 | DOD ID CA-51                                             | DoD Root CA 3                         | 11/22/2016 13:49 | 11/23/2022 13:49 | 129                                      |
| 00014 | DOD ID CA-52                                             | DoD Root CA 3                         | 11/22/2016 13:49 | 11/23/2022 13:49 | 12A                                      |
| 00015 | DOE SSP CA                                               | Entrust Managed   Services Root CA    | 5/29/2012 14:13  | 4/29/2019 14:43  | 447FFCD7                                 |
| 00016 | DOE SSP CA                                               | Entrust Managed   Services Root CA    | 3/1/2018 14:13   | 6/1/2025 14:43   | 4480CB97                                 |
| 00017 | Entrust Managed   Services SSP CA                        | Entrust Managed   Services Root CA    | 5/9/2009 15:32   | 5/9/2019 14:02   | 447F9D1F                                 |
| 00018 | Entrust Managed   Services SSP CA                        | Entrust Managed   Services Root CA    | 7/30/2015 16:37  | 7/23/2025 16:36  | 448063D5                                 |
| 00019 | GPO SCA                                                  | GPO PCA                               | 12/15/2010 11:30 | 12/15/2020 11:43 | 40D7D0EE                                 |
| 00020 | GPO SCA                                                  | GPO PCA                               | 10/14/2015 14:20 | 10/14/2025 14:37 | 40D86A4F                                 |
| 00021 | HHS-FPKI-Intermediate-CA-E1                              | Entrust Managed   Services Root CA    | 3/7/2013 17:41   | 5/9/2019 14:02   | 44801668                                 |
| 00022 | HHS-FPKI-Intermediate-CA-E1                              | Entrust Managed   Services Root CA    | 12/20/2016 15:40 | 7/20/2025 16:10  | 44809A90                                 |
| 00023 | NASA Operational CA                                      | US Treasury Root CA                   | 1/22/2011 13:39  | 1/22/2021 14:09  | 4A61D2A5                                 |
| 00024 | NASA Operational CA                                      | US Treasury Root CA                   | 6/13/2015 14:24  | 6/13/2025 14:54  | 4E398116                                 |
| 00025 | Naval Reactors SSP   Agency CA G3                        | Symantec SSP   Intermediate CA - G4   | 12/10/2015 0:00  | 11/11/2024 23:59 | 18876CD9FFD738AB7E69350ECC9D41F8         |
| 00026 | NRC SSP Agency CA G3                                     | Symantec SSP   Intermediate CA - G4   | 11/25/2014 0:00  | 11/11/2024 23:59 | 100F05DD316CA819D9D39FEBC661B326         |
| 00027 | OCIO CA                                                  | US Treasury Root CA                   | 9/12/2010 14:46  | 9/12/2020 15:16  | 4A61D147                                 |
| 00028 | OCIO CA                                                  | US Treasury Root CA                   | 11/7/2010 14:46  | 11/7/2020 15:16  | 4A61D192                                 |
| 00029 | OCIO CA                                                  | US Treasury Root CA                   | 4/19/2015 15:17  | 4/19/2025 15:47  | 4E398101                                 |
| 00030 | ORC SSP 3                                                | Federal Common Policy   CA            | 1/12/2011 0:54   | 1/12/2021 0:52   | 2C2                                      |
| 00031 | ORC SSP 4                                                | Federal Common Policy   CA            | 8/31/2015 15:14  | 1/21/2024 16:36  | 2EF9                                     |
| 00032 | Social Security   Administration Certification Authority | US Treasury Root CA                   | 2/16/2011 23:29  | 2/16/2021 23:59  | 4A61D2BA                                 |
| 00033 | Social Security   Administration Certification Authority | US Treasury Root CA                   | 4/19/2015 15:04  | 4/19/2025 15:34  | 4E3980EF                                 |
| 00034 | U.S. Department of   Education Agency CA - G3            | VeriSign SSP   Intermediate CA - G3   | 10/20/2011 0:00  | 10/19/2018 23:59 | A8159718ADC92AC10A506ACB577CEAB          |
| 00035 | U.S. Department of   Education Agency CA - G4            | Symantec SSP   Intermediate CA - G4   | 7/21/2015 0:00   | 11/11/2024 23:59 | 224AD7D35A9D34350671F9B8BE45A23A         |
| 00036 | U.S. Department of   State PIV CA                        | U.S. Department of   State AD Root CA | 9/30/2009 23:18  | 9/30/2019 23:48  | 40DA5F3D                                 |
| 00037 | U.S. Department of   State PIV CA2                       | U.S. Department of   State AD Root CA | 8/3/2016 16:13   | 8/3/2026 16:43   | 51B02402                                 |
| 00038 | U.S. Department of   Transportation Agency CA G4         | Symantec SSP   Intermediate CA - G4   | 12/11/2014 0:00  | 11/11/2024 23:59 | 61A90F3E5FF532F9FE6209D931279A82         |
| 00039 | USPTO_INTR_CA1                                           | Federal Bridge CA   2016              | 12/15/2016 19:04 | 12/15/2019 19:04 | 2E4E311804B49D0BFFF0E3B6A9254FC4D1A97F32 |
| 00040 | USPTO_INTR_CA1                                           | Federal Bridge CA   2016              | 4/23/2018 17:17  | 12/15/2019 17:16 | 6E6C011D8D83D73D918FC917C2AC515A472EF2DF |
| 00041 | USPTO_INTR_CA1                                           | USPTO_INTR_CA1                        | 6/24/2010 20:45  | 6/24/2030 21:15  | 4C296F46                                 |
| 00042 | USPTO_INTR_CA1                                           | USPTO_INTR_CA1                        | 4/7/2018 12:16   | 12/7/2029 12:46  | 4C296F47                                 |
| 00043 | Verizon SSP CA A2                                        | Federal Common Policy   CA            | 12/6/2016 16:12  | 12/6/2026 16:09  | 403C                                     |
| 00044 | Veterans Affairs CA   B3                                 | Verizon SSP CA A2                     | 12/15/2017 17:56 | 12/15/2027 17:56 | 5ECB874A1B24B1113848E40E76DC3EA4449624FE |
| 00045 | Veterans Affairs User   CA B1                            | Betrusted Production   SSP CA A1      | 8/7/2008 15:08   | 8/7/2018 15:04   | 1C5E                                     |
| 00046 | Veterans Affairs User   CA B1                            | Verizon SSP CA A2                     | 1/25/2017 4:59   | 1/25/2027 4:59   | 251EA36536CFEBB0E9D1334D0CB96102BAB16589 |

If you feel another CA should be included, let us know!


### System requirements

Python v3.x is required to run the Trust Store Management Script. Additionally, on Windows, OpenSSL needs to be installed with an environment path variable set to support .p7b file generation.

Your feedback is valued to us. If you have any questions or recommended updates for this script, please contact us at fpki@gsa.gov.

### Running the script
1. [Download](#download-location) the script .zip file and verify its hash
1. Unpack the zip file to your Desktop. If a different location is selected, you will need to edit default path variables *CONFIG_PATH* and *CERT_PATH* inside certLoader.py script 
1. View and optionally [edit](#how-it-works) the targets.json file to confirm or update the list of certification authorities targeted for installation.
1. Execute the script by running the following command-line commands
- On Windows:
    ```
    > cd Desktop\Trust_Store_Mangagement_Script_V1
    > certLoader.py
    ```
- On macOS:
    ```
    $ cd Desktop/Trust_Store_Mangagement_Script_V1
    $ certLoader.py
    ```
1. The resulting output file will be placed on your Desktop, unless the location was changed in certLoader.py

#### Sample Output - Windows

```
C:\Users\Joe.User\Desktop\Trust_Store_Mangagement_Script_V1>certLoader.py

You have selected the following CAs for installation:

ID: 00001
SUBJECT: Betrusted Production SSP CA A1
ISSUER: Federal Common Policy CA
VALID FROM: 12/9/2010 19:55
VALID TO: 12/9/2020 19:49
SERIAL: 19A
THUMBPRINT: 0601bbdad5a28231bc9436750b4f3a484bab06c3


ID: 00002
SUBJECT: Bureau of the Census Agency CA
ISSUER: Symantec SSP Intermediate CA - G4
VALID FROM: 7/30/2015 0:00
VALID TO: 11/11/2024 23:59
SERIAL: 2355994850457C656B1B9A58E3FC3F98
THUMBPRINT: 4f75d3b2eec610d0948006ea73740c738068be17


ID: 00003
SUBJECT: Department of Veterans Affairs CA
ISSUER: US Treasury Root CA
VALID FROM: 8/28/2012 13:47
VALID TO: 8/28/2022 14:17
SERIAL: 4E397F22
THUMBPRINT: 519d3222a15eee034980fc0da727314f70af78c0


ID: 00004
SUBJECT: Department of Veterans Affairs CA
ISSUER: US Treasury Root CA
VALID FROM: 10/17/2015 14:01
VALID TO: 10/17/2025 14:31
SERIAL: 4E398179
THUMBPRINT: e2edb0df1fe8068717a08e38741b5bc4c38029d0


ID: 00005
SUBJECT: DHS CA4
ISSUER: US Treasury Root CA
VALID FROM: 1/21/2011 19:11
VALID TO: 1/21/2021 19:41
SERIAL: 4A61D293
THUMBPRINT: a31a5df2f1c1019b9cf5b7ca4e3b26650b9ca93f


ID: 00006
SUBJECT: DHS CA4
ISSUER: US Treasury Root CA
VALID FROM: 6/13/2015 14:35
VALID TO: 6/13/2025 15:05
SERIAL: 4E398128
THUMBPRINT: 49ae4f027419a3eb227e4cd4ccf4ff1bc75213b6


ID: 00007
SUBJECT: DOD ID CA-41
ISSUER: DoD Root CA 3
VALID FROM: 11/9/2015 16:13
VALID TO: 11/9/2021 16:13
SERIAL: 18
THUMBPRINT: fee543483ac0acb519688715237d3b57b9d34755


ID: 00008
SUBJECT: DOD ID CA-42
ISSUER: DoD Root CA 3
VALID FROM: 11/9/2015 16:15
VALID TO: 11/9/2021 16:15
SERIAL: 19
THUMBPRINT: 8b125cfc2716558d716a9f87db7ca8316d11236e


ID: 00009
SUBJECT: DOD ID CA-43
ISSUER: DoD Root CA 3
VALID FROM: 11/9/2015 16:16
VALID TO: 11/9/2021 16:16
SERIAL: 1A
THUMBPRINT: 98d3e33b50b8f6acff8c6020e29ff967eb0e9c18


ID: 00010
SUBJECT: DOD ID CA-44
ISSUER: DoD Root CA 3
VALID FROM: 11/9/2015 16:18
VALID TO: 11/9/2021 16:18
SERIAL: 1B
THUMBPRINT: cbb492c4e8a52f024772ba4e53d47391b98fcef0


ID: 00011
SUBJECT: DOD ID CA-49
ISSUER: DoD Root CA 3
VALID FROM: 11/22/2016 13:48
VALID TO: 11/23/2022 13:48
SERIAL: 127
THUMBPRINT: 6cd6e8bd7acd2f08e21693988a309eca6772c134


ID: 00012
SUBJECT: DOD ID CA-50
ISSUER: DoD Root CA 3
VALID FROM: 11/22/2016 13:48
VALID TO: 11/23/2022 13:48
SERIAL: 128
THUMBPRINT: 5e2e392c6ca55e9bd3f522969ffa6b3657a5d910


ID: 00013
SUBJECT: DOD ID CA-51
ISSUER: DoD Root CA 3
VALID FROM: 11/22/2016 13:49
VALID TO: 11/23/2022 13:49
SERIAL: 129
THUMBPRINT: f0a49bcf0fd1fc1521b31b2796fb829780050ee4


ID: 00014
SUBJECT: DOD ID CA-52
ISSUER: DoD Root CA 3
VALID FROM: 11/22/2016 13:49
VALID TO: 11/23/2022 13:49
SERIAL: 12A
THUMBPRINT: 82118887716a07449fadd643eef739f04981087c


ID: 00015
SUBJECT: DOE SSP CA
ISSUER: Entrust Managed Services Root CA
VALID FROM: 5/29/2012 14:13
VALID TO: 4/29/2019 14:43
SERIAL: 447FFCD7
THUMBPRINT: 8240f1f7ac2ae97ca8eb913f15dba6a1c1f6d861


ID: 00016
SUBJECT: DOE SSP CA
ISSUER: Entrust Managed Services Root CA
VALID FROM: 3/1/2018 14:13
VALID TO: 6/1/2025 14:43
SERIAL: 4480CB97
THUMBPRINT: 607a8f5d6db771d96088e8b62ff2ded30bcdb153


ID: 00017
SUBJECT: Entrust Managed Services SSP CA
ISSUER: Entrust Managed Services Root CA
VALID FROM: 5/9/2009 15:32
VALID TO: 5/9/2019 14:02
SERIAL: 447F9D1F
THUMBPRINT: b67e30bef74c37f9716b00bcdc5c859f73925962


ID: 00018
SUBJECT: Entrust Managed Services SSP CA
ISSUER: Entrust Managed Services Root CA
VALID FROM: 7/30/2015 16:37
VALID TO: 7/23/2025 16:36
SERIAL: 448063D5
THUMBPRINT: dec01bf40c153fbc38bf2ca766b04f9dfbda3064


ID: 00019
SUBJECT: GPO SCA
ISSUER: GPO PCA
VALID FROM: 12/15/2010 11:30
VALID TO: 12/15/2020 11:43
SERIAL: 40D7D0EE
THUMBPRINT: 4c1a6548bb9e9a7854894d0437357019fb2efbfe


ID: 00020
SUBJECT: GPO SCA
ISSUER: GPO PCA
VALID FROM: 10/14/2015 14:20
VALID TO: 10/14/2025 14:37
SERIAL: 40D86A4F
THUMBPRINT: b914fda0c3a0ee78f8fa284d3c82288ce2f60ea5


ID: 00021
SUBJECT: HHS-FPKI-Intermediate-CA-E1
ISSUER: Entrust Managed Services Root CA
VALID FROM: 3/7/2013 17:41
VALID TO: 5/9/2019 14:02
SERIAL: 44801668
THUMBPRINT: 35f9516b2ccc54959294584e72b272efcca965a3


ID: 00022
SUBJECT: HHS-FPKI-Intermediate-CA-E1
ISSUER: Entrust Managed Services Root CA
VALID FROM: 12/20/2016 15:40
VALID TO: 7/20/2025 16:10
SERIAL: 44809A90
THUMBPRINT: d5e311406437c35a79bc023c2bbb57049f5d8f77


ID: 00023
SUBJECT: NASA Operational CA
ISSUER: US Treasury Root CA
VALID FROM: 1/22/2011 13:39
VALID TO: 1/22/2021 14:09
SERIAL: 4A61D2A5
THUMBPRINT: 76a6eaa852710e00b368c41080e6131140aaf189


ID: 00024
SUBJECT: NASA Operational CA
ISSUER: US Treasury Root CA
VALID FROM: 6/13/2015 14:24
VALID TO: 6/13/2025 14:54
SERIAL: fe7572bbde7b7f44152acc8e1715c18714dc9d63
THUMBPRINT: fe7572bbde7b7f44152acc8e1715c18714dc9d63


ID: 00025
SUBJECT: Naval Reactors SSP Agency CA G3
ISSUER: Symantec SSP Intermediate CA - G4
VALID FROM: 12/10/2015 0:00
VALID TO: 11/11/2024 23:59
SERIAL: 18876CD9FFD738AB7E69350ECC9D41F8
THUMBPRINT: 50e722c3b05485b216bbc02eb1628e2593a5565d


ID: 00026
SUBJECT: NRC SSP Agency CA G3
ISSUER: Symantec SSP Intermediate CA - G4
VALID FROM: 11/25/2014 0:00
VALID TO: 11/11/2024 23:59
SERIAL: 100F05DD316CA819D9D39FEBC661B326
THUMBPRINT: e40bee41cf7afa2ddba4eb10ff3a39f81ec48d20


ID: 00027
SUBJECT: OCIO CA
ISSUER: US Treasury Root CA
VALID FROM: 9/12/2010 14:46
VALID TO: 9/12/2020 15:16
SERIAL: 4A61D147
THUMBPRINT: f9299790eb271125fd91e661cede4ee202d7e758


ID: 00028
SUBJECT: OCIO CA
ISSUER: US Treasury Root CA
VALID FROM: 11/7/2010 14:46
VALID TO: 11/7/2020 15:16
SERIAL: 4A61D192
THUMBPRINT: 918a68d87fb6011afe3666076319ed0462df0940


ID: 00029
SUBJECT: OCIO CA
ISSUER: US Treasury Root CA
VALID FROM: 4/19/2015 15:17
VALID TO: 4/19/2025 15:47
SERIAL: 4E398101
THUMBPRINT: 5ad254c3ecebb5b7e108caa0cc8030598a7b7709


ID: 00030
SUBJECT: ORC SSP 3
ISSUER: Federal Common Policy CA
VALID FROM: 1/12/2011 0:54
VALID TO: 1/12/2021 0:52
SERIAL: 2C2
THUMBPRINT: bbfa5abd8a09d73be1fa30363f87402fec5316f9


ID: 00031
SUBJECT: ORC SSP 4
ISSUER: Federal Common Policy CA
VALID FROM: 8/31/2015 15:14
VALID TO: 1/21/2024 16:36
SERIAL: 2EF9
THUMBPRINT: 3a70323069a4c41bc95663152e9ccc7111bb0623


ID: 00032
SUBJECT: Social Security Administration Certification Authority
ISSUER: US Treasury Root CA
VALID FROM: 2/16/2011 23:29
VALID TO: 2/16/2021 23:59
SERIAL: 4A61D2BA
THUMBPRINT: b4b209aade830834c9b5c2f815021d28dc381fe1


ID: 00033
SUBJECT: Social Security Administration Certification Authority
ISSUER: US Treasury Root CA
VALID FROM: 4/19/2015 15:04
VALID TO: 4/19/2025 15:34
SERIAL: 4E3980EF
THUMBPRINT: bb6c62e648d503f1beab75ef5f69b17256175993


ID: 00034
SUBJECT: U.S. Department of Education Agency CA - G3
ISSUER: VeriSign SSP Intermediate CA - G3
VALID FROM: 10/20/2011 0:00
VALID TO: 10/19/2018 23:59
SERIAL: A8159718ADC92AC10A506ACB577CEAB
THUMBPRINT: 18586c1d3f97fc2d22221fe9f16ac2b2cca88757


ID: 00035
SUBJECT: U.S. Department of Education Agency CA - G4
ISSUER: Symantec SSP Intermediate CA - G4
VALID FROM: 7/21/2015 0:00
VALID TO: 11/11/2024 23:59
SERIAL: 224AD7D35A9D34350671F9B8BE45A23A
THUMBPRINT: 69e2abc173047f844e3f53cb2cbd138ba9063de8


ID: 00036
SUBJECT: U.S. Department of State PIV CA
ISSUER: U.S. Department of State AD Root CA
VALID FROM: 9/30/2009 23:18
VALID TO: 9/30/2019 23:48
SERIAL: 40DA5F3D
THUMBPRINT: 1a4854e18ffbbb2b1582b878cd5cbc242e4e3057


ID: 00037
SUBJECT: U.S. Department of State PIV CA2
ISSUER: U.S. Department of State AD Root CA
VALID FROM: 8/3/2016 16:13
VALID TO: 8/3/2026 16:43
SERIAL: 51B02402
THUMBPRINT: ffe07fb428bcef4bf38ebbfae1e42339e03e7756


ID: 00038
SUBJECT: U.S. Department of Transportation Agency CA G4
ISSUER: Symantec SSP Intermediate CA - G4
VALID FROM: 12/11/2014 0:00
VALID TO: 11/11/2024 23:59
SERIAL: 61A90F3E5FF532F9FE6209D931279A82
THUMBPRINT: dc5b590800765864587902af983c21a7209be320


ID: 00039
SUBJECT: USPTO_INTR_CA1
ISSUER: Federal Bridge CA 2016
VALID FROM: 12/15/2016 19:04
VALID TO: 12/15/2019 19:04
SERIAL: 2E4E311804B49D0BFFF0E3B6A9254FC4D1A97F32
THUMBPRINT: 85fa67194d854d7c41a06c82f8a1376d1508e8cf


ID: 00040
SUBJECT: USPTO_INTR_CA1
ISSUER: Federal Bridge CA 2016
VALID FROM: 4/23/2018 17:17
VALID TO: 12/15/2019 17:16
SERIAL: 6E6C011D8D83D73D918FC917C2AC515A472EF2DF
THUMBPRINT: 0704ea9633a45a9a39123bac28be01078c6bfd3a


ID: 00041
SUBJECT: USPTO_INTR_CA1
ISSUER: USPTO_INTR_CA1
VALID FROM: 6/24/2010 20:45
VALID TO: 6/24/2030 21:15
SERIAL: 4C296F46
THUMBPRINT: 0fe0c471ff7c279a1adb8f4c1f240d64c7a04894


ID: 00042
SUBJECT: USPTO_INTR_CA1
ISSUER: USPTO_INTR_CA1
VALID FROM: 4/7/2018 12:16
VALID TO: 12/7/2029 12:46
SERIAL: 4C296F47
THUMBPRINT: bc67b9e65ee05c3742c27187259ded3e6112a587


ID: 00043
SUBJECT: Verizon SSP CA A2
ISSUER: Federal Common Policy CA
VALID FROM: 12/6/2016 16:12
VALID TO: 12/6/2026 16:09
SERIAL: 403C
THUMBPRINT: a9d3a8ac016dba9fa12685bf59dcc39f5dcaf781


ID: 00044
SUBJECT: Veterans Affairs CA B3
ISSUER: Verizon SSP CA A2
VALID FROM: 12/15/2017 17:56
VALID TO: 12/15/2027 17:56
SERIAL: 5ECB874A1B24B1113848E40E76DC3EA4449624FE
THUMBPRINT: fddb25c3cda647fd56954b58de95878422fb9c11


ID: 00045
SUBJECT: Veterans Affairs User CA B1
ISSUER: Betrusted Production SSP CA A1
VALID FROM: 8/7/2008 15:08
VALID TO: 8/7/2018 15:04
SERIAL: 1C5E
THUMBPRINT: 7bd3f48d67d48d52c0d0b26fe73cb55ae4a6380d


ID: 00046
SUBJECT: Veterans Affairs User CA B1
ISSUER: Verizon SSP CA A2
VALID FROM: 1/25/2017 4:59
VALID TO: 1/25/2027 4:59
SERIAL: 251EA36536CFEBB0E9D1334D0CB96102BAB16589
THUMBPRINT: 671461948b8ef765fe5e1248222af3fcdd457564


An installation file has been created at: C:\Users\Joe.User\Desktop\tsmt-output.p7b
```

The sample output file created in the example above is found [here](TSMS-V1/sample-tsmt-output.p7b).

#### Sample Output - macOS
[TO-DO] - Capture macOS output.

The sample output file created in the example above is found [here](TSMS-V1/sample-tsmt-output.mobileconfig).

    
### FAQs
#### How often will it be updated?
We anticipate this script being updated quarterly. 

#### Who can I contact for help?
Email us at fpki@gsa.gov. 
