# Twomes Dataset Assendorp 2021 (metadata only; real data follows)

Dataset containing anonimized energy and temperature data from homes in the Assendorp neighbourhood in Zwolle, the Netherlands

*The dataset is currently in review to ensure it meet our standards for anonimization. Here, you can already view the metadata, so you know what you may expect.*

## Table of contents
* [General info](#general-info)
* [Subject Recruitment](#recruitment)
* [Inclusion criteria](#inclusion-criteria)
* [Data](#data) 
* [Status](#status)
* [License](#license)
* [Credits](#credits)

## General info

This repository will contain an anonimized dataset comprising time series measurement data about enery and temperatures in residential homes (mostly) in the Assendorp neighbourhood in Zwolle, the Netherlands, obtained during the heating season of 2021-2022 in the Twomes research project.

**Note**: [Git LFS](https://git-lfs.github.com/) is required to clone big CSV files

## Recruitment 

Subjects were recruitedfrom the municipality of Zwolle during autumn 2021. Recruitment was primarily targeted at the Assendorp neighbourhood, via [50 Tinten Groen Assendorp](https://50tintengroenassendorp.nl/), e.g. via a [news message](https://50tintengroenassendorp.nl/40-13-september-2021-warmtewachters-gezocht/). In November 2021, additional subjects were recruited amongst colleagues and students of Windesheim University of Applied Sciences, e.g. via an [internal message](https://liveadminwindesheim.sharepoint.com/sites/rvd-Domein-Techniek/Lists/Announcements/DispForm.aspx?ID=181). 

Subjects could volunteer to participate and give informed consent by filling out this [generic online recruitment survey](data_management/recruitment/QualtricsSurvey_Twomes_v10.pdf) ([this survey is also available in XML-format](data_management/recruitment/QualtricsSurvey_Twomes_v10.qsf)). Subjects that satified all [inclusion criteria](#inclusion-criteria) were also asked to fill out this [online survey to provide their static Bluetooth MAC addressto us](data_management/recruitment\Survey_Twomes_Bluetooth-v10.pdf) ([this survey is also available in XML-format](data_management/recruitment/Survey_Twomes_Bluetooth-v10.qsf)) and were asked to forward this latter survey to the other members of the household with a smartphone.   

## Inclusion criteria

Inclusion criteria were:
* the home address lies in the muncipality of Zwolle, the Netherlands;
* the home is equipped with an internet connection and wireless internet (Wi-Fi);
* the home is heated by a gas-fired heating boiler that is controlled by a termostat and is not predominantly heated via other means;
* a smart energy meter is installed in the home;
* at least one of the occupants has an Android/iOS smartphone.

## Data management

We documented our [Data Management Plan](data_management/DMP_online_Twomes_data_collection_Zwolle_winter_2021-2022.pdf) online. The privacy policy (in Dutch) is available online as well in a layered structure: [short summary](data_management/privacy/privacy.html), [summary](data_management/privacy/privacy-summary.html) and [full version](data_management/privacy/privacy-full.html). 

## Data

In the sections below, the data pre-processing and data formats used in the data files will be described.

### Homes 

TODO: describe

### Measurement Devices 

We used the following measurement device types to collect data. Some devices consisted of a main device and one or two satellite devices.

| Device type name             | Category                                                | Main device repo                                                                                           | Sattelite device 2 repo                                                                          | Sattelite device 2 repo                                                                              |
| ---------------------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| `OpenTherm-Monitor`          | comfort + installation + occupancy                      | [twomes-opentherm-monitor-firmware](https://github.com/energietransitie/twomes-opentherm-monitor-firmware) |                                                                                                  |                                                                                                      |
| `DSMR-P1-gateway`            | energy                                                  | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               |                                                                                                  |                                                                                                      |
| `DSMR-P1-gateway-Tin`        | energy + comfort                                        | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) |                                                                                                      |
| `DSMR-P1-gateway-TinTsTr`    | energy + comfort + installation                         | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) | [twomes-boiler-monitor-firmware](https://github.com/energietransitie/twomes-boiler-monitor-firmware) |
| `DSMR-P1-gateway-TinTsTrCO2` | energy + comfort + installation + occupancy/ventilation | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) | [twomes-boiler-monitor-firmware](https://github.com/energietransitie/twomes-boiler-monitor-firmware) |

### Date and time information

All timestamps were measured in [Unix time](https://en.wikipedia.org/wiki/Unix_time) format, using device clocks regularly synchronized via NTP with the correct UTC time. Setting the local device clock to the proper UTC time via NTP was one of the first steps performed by the measurement devices after they were connected to the internet via the home Wi-Fi network of a subject. Each measurement device synchronized its device clock via NTP every 6 hours. Uploads of measurement data (which could contain more than one measurement) were timestamped both by the measurement device according to the local device clock and by the server. We did not yet check for deviations between the last device timestamp of a measurement upload and the upload timestamp at the server.

Timestamps were converted to a timezone-aware `pandas.Timestamp` value, in the [Europe/Amsterdam](https://en.wikipedia.org/wiki/Time_in_the_Netherlands) timezone. In the csv files we use [ISO 8601 format with time offset](https://en.wikipedia.org/wiki/ISO_8601): `YYYY-MM-DDThh:mm:ss±hhmm`.

### Raw measurements 
 Raw masurements will be available in the folder [/raw-measurements/](/raw-measurements/). be avaiable in three formats:

 - [twomes_raw_measurements.parquet](/raw-measurements/twomes_raw_measurements.parquet): a single [parquet](https://parquet.apache.org/) file with all 23 homes for which we have more than 3 weeks data;
 - 8nnnnn_raw_measurements.parquet: 23 [parquet](https://parquet.apache.org/) files, one for each home;
 - 8nnnnn_raw_measurements.zip: 23 [zip](https://en.wikipedia.org/wiki/ZIP_(file_format))ped [csv](https://en.wikipedia.org/wiki/Comma-separated_values) files, one for each home;

All measurement data is structured according to the table below. By importing the parquet variant using [pandas.read_parquet()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html), you automatically get a DataFrame wih the recommended indices and data types. 

Alternatively, you can also read the zipped csv files, but this typically takes much longer. You can use the code below to endup with a DataFrame with the recommended indices and data types:

```
TODO: insert pandas.read_csv() code here
```

| **Index/Column** | **Name** | **Type**     | **Description**                                                                                                                                       |
| ---------- | --------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| index       | `id`            | `category`   | unique code of the home                                                                                                                               |
| index       | `device_name`   | `category`   | unique name of the measurement device                                                                                                                 |
| index       | `source`        | `category`   | [device type name](###measurement-devices) of the measurement device                                                                                  |
| index        | `timestamp`     | `Timestamp` | start of the interval (timezone aware) |
| index       | `property`      | `category`   | property name of the measurement                                                                                                                      |
| column      | `value`         | `object`     | value of the measurement                                                                                                                              |
| column      | `unit`          | `category`   | unit of the measurement value                                                                                                                         |

### Raw propertes 
 In the folder [/raw-properties/](/raw-properties/) we will make various measured properties available in an 'unstacked' format with each property in its own column and an appropriate datatype. Similar to measurements, we will make data available in three formats:

 - [twomes_raw_properties.parquet](/raw-properties/twomes_raw_measurements.parquet): a single [parquet](https://parquet.apache.org/) file with all 23 homes for which we have more than 3 weeks data;
 - 8nnnnn_raw_properties.parquet: 23 [parquet](https://parquet.apache.org/) files, one for each home;
 - 8nnnnn_raw_properties.zip: 23 [zip](https://en.wikipedia.org/wiki/ZIP_(file_format))ped [csv](https://en.wikipedia.org/wiki/Comma-separated_values) files, one for each home;

All property data is structured according to the table below. By importing the parquet variant using [pandas.read_parquet()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html), you automatically get a DataFrame wih the recommended indices and data types. 

Alternatively, you can also read the zipped csv files, but this typically takes much longer. You can use the code below to endup with a DataFrame with the recommended indices and data types:

```
TODO: insert pandas.read_csv() code here
```

| **Index/Column** | **Name** | **Type**     | **Description**                                                                                                                                       |
| ---------- | --------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| index       | `id`            | `category`   | unique code of the home                                                                                                                               |
| index       | `source`        | `category`   | [device type name](###measurement-devices) of the measurement device                                                                                  |
| index        | `timestamp`     | `Timestamp` | start of the interval (timezone aware) |
| column       | property_1; see property table below      | data_type_1   | measured value of this property |
| column       | property2 | data_type_2   | measured value of this property |
| ...       | ...      | ...   | ... |
| column       | property_n      | data_type_n   | masured value of this property |


### Measured Properties 

Below is a table that lists all properties that were measured, the data type in the [raw-properties](#raw-poperties) DataFrame, the measurement unit, the measurement interval, the source device and sensor that measured it, as well as the the property name and value format as retrieved from the Twomes database.

| Property                 | Type      | Unit                                                                                                                                                                                                                                                       | Measurement interval \[h:mm:ss\] | Description                                | Source                       | Sensor /<br />[OpenTherm ID/byte/bit)](https://github.com/Nodo-Domotica/OpenTherm-GateWay/blob/master/OT-Protocol-Specification-v2-2.pdf) /<br />OBIS reference | Database property          | [Database format](https://en.wikipedia.org/wiki/Printf_format_string) |
| ------------------------ | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ------------------------------------------ | ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | --------------------------------------------------------------------- |
| `co2__ppm`                | `float32` | ppm                                                                                                                                                                                                                                                        | 0:05:00                          | CO₂ concentration                          | `DSMR-P1-gateway-TinTsTrCO2` | [SCD41](https://sensirion.com/products/catalog/SCD41/)                                                                                                          | `CO2concentration`         | %d                                                                    |
| `rel_humidity__0`         | `float32` | -                                                                                                                                                                                                                                                          | 0:05:00                          | relative humidity                          | `DSMR-P1-gateway-TinTsTrCO2` | [SCD41](https://sensirion.com/products/catalog/SCD41/)                                                                                                          | `humidity`                 | %d                                                                    |
| `temp_in__degC`           | `float32` | °C                                                                                                                                                                                                                                                         | 0:05:00                          | air temperature                            | `DSMR-P1-gateway-TinTsTrCO2` | [SCD41](https://sensirion.com/products/catalog/SCD41/)                                                                                                          | `roomTempCO2`              | %.1f                                                                  |
| `temp_in__degC`           | `float32` | °C                                                                                                                                                                                                                                                         | 0:05:00                          | air temperature                            | `DSMR-P1-gateway-TinTsTr`    | [Si7051](https://www.silabs.com/sensors/temperature/si705x/device.si7051)                                                                                       | `roomTemp`                 | %.1f                                                                  |
| `temp1__degC`             | `float32` | °C                                                                                                                                                                                                                                                         | 0:00:10                          | temperature of hydronic supply/return pipe | `DSMR-P1-gateway-TinTsTr`    | [DS18B20](https://www.maximintegrated.com/en/products/sensors/DS18B20.html)                                                                                     | `boilerTemp1`              | %.1f                                                                  |
| `temp2__degC`             | `float32` | °C                                                                                                                                                                                                                                                         | 0:00:10                          | temperature of hydronic supply/return pipe | `DSMR-P1-gateway-TinTsTr`    | [DS18B20](https://www.maximintegrated.com/en/products/sensors/DS18B20.html)                                                                                     | `boilerTemp2`              | %.1f                                                                  |
| `heartbeat`              | `Int16`   | -                                                                                                                                                                                                                                                          | 0:10:00                          | measurement system heartbeat               | `OpenTherm-Monitor`          | ESP32                                                                                                                                                           | `heartbeat`                | %d                                                                    |
| `ch__bool`                | `Int8`    | bool                                                                                                                                                                                                                                                       | 0:00:30                          | STATUS/CH mode                             | `OpenTherm-Monitor`          | 0/LB/1                                                                                                                                                          | `isCentralHeatingModeOn`   | 0/1                                                                   |
| `dhw__bool`               | `Int8`    | bool                                                                                                                                                                                                                                                       | 0:00:30                          | STATUS/DHW mode                            | `OpenTherm-Monitor`          | 0/LB/2                                                                                                                                                          | `isDomesticHotWaterModeOn` | 0/1                                                                   |
| `flame__bool`             | `Int8`    | bool                                                                                                                                                                                                                                                       | 0:00:30                          | STATUS /Flame status                       | `OpenTherm-Monitor`          | 0/LB/3                                                                                                                                                          | `isBoilerFlameOn`          | 0/1                                                                   |
| `mod_max__0`              | `Int8`    | -                                                                                                                                                                                                                                                          | 0:00:30                          | CAPACITY SETTING                           | `OpenTherm-Monitor`          | 14                                                                                                                                                              | `maxModulationLevel`       | %d                                                                    |
| `cap_max__kW`             | `Int8`    | kW                                                                                                                                                                                                                                                         | 0:00:30                          | MAX CAPACITY                               | `OpenTherm-Monitor`          | 15/LB                                                                                                                                                           | `maxBoilerCap`             | %d                                                                    |
| `mod_min__0`              | `Int8`    | -                                                                                                                                                                                                                                                          | 0:00:30                          | MIN-MOD-LEVEL                              | `OpenTherm-Monitor`          | 15/HB                                                                                                                                                           | `minModulationLevel`       | %d                                                                    |
| `temp_set__degC`          | `float32` | °C                                                                                                                                                                                                                                                         | 0:05:00                          | ROOM SETPOINT                              | `OpenTherm-Monitor`          | 16                                                                                                                                                              | `roomSetpointTemp`         | %.2f                                                                  |
| `mod__0`                  | `Int8`    | -                                                                                                                                                                                                                                                          | 0:00:30                          | RELATIVE MODULATION LEVEL                  | `OpenTherm-Monitor`          | 17                                                                                                                                                              | `relativeModulationLevel`  | %d                                                                    |
| `temp_in__degC`           | `float32` | °C                                                                                                                                                                                                                                                         | 0:05:00                          | ROOM TEMPERATURE                           | `OpenTherm-Monitor`          | 24                                                                                                                                                              | `roomTemp`                 | %.2f                                                                  |
| `temp_ch_max__degC`       | `float32` | °C                                                                                                                                                                                                                                                         | 0:05:00                          | MAX CH WATER SETPOINT                      | `OpenTherm-Monitor`          | 57                                                                                                                                                              | `boilerMaxSupplyTemp`      | %.2f                                                                  |
| `temp_sup__degC`          | `float32` | °C                                                                                                                                                                                                                                                         | 0:00:10                          | BOILER WATER TEMP.                         | `OpenTherm-Monitor`          | 25                                                                                                                                                              | `boilerSupplyTemp`         | %.2f                                                                  |
| `temp_ret__degC`          | `float32` | °C                                                                                                                                                                                                                                                         | 0:00:10                          | RETURN WATER TEMPERATURE                   | `OpenTherm-Monitor`          | 28                                                                                                                                                              | `boilerReturnTemp`         | %.2f                                                                  |
| `presence__dBm_csv`       | `str`     | \[dBm\]                                                                                                                                                                                                                                                    | 1:00:00                          | Bluetooth presence                         | `OpenTherm-Monitor`          | ESP32                                                                                                                                                           | `listRSSI`                 | %d                                                                    |
| `heartbeat`              | `Int16`   | -                                                                                                                                                                                                                                                          | 0:10:00                          | measurement system heartbeat               | `DSMR-P1-gateway`            | ESP32                                                                                                                                                           | `heartbeat`                | %d                                                                    |
| `e_use_lo_cum__kWh`       | `float64` | kWh                                                                                                                                                                                                                                                        | 0:05:00                          | electricity meter reading                  | `DSMR-P1-gateway`            | 1-0:1.8.1                                                                                                                                                       | `eMeterReadingSupplyLow`   | %.3f                                                                  |
| `e_use_hi_cum__kWh`       | `float64` | kWh                                                                                                                                                                                                                                                        | 0:05:00                          | electricity meter reading                  | `DSMR-P1-gateway`            | 1-0:1.8.2                                                                                                                                                       | `eMeterReadingSupplyHigh`  | %.3f                                                                  |
| `e_ret_lo_cum__kWh`       | `float64` | kWh                                                                                                                                                                                                                                                        | 0:05:00                          | electricity meter reading                  | `DSMR-P1-gateway`            | 1-0:2.8.1                                                                                                                                                       | `eMeterReadingReturnLow`   | %.3f                                                                  |
| `e_ret_hi_cum__kWh`       | `float64` | kWh                                                                                                                                                                                                                                                        | 0:05:00                          | electricity meter reading                  | `DSMR-P1-gateway`            | 1-0:2.8.2                                                                                                                                                       | `eMeterReadingReturnHigh`  | %.3f                                                                  |
| `e_timestamp__YYMMDDhhmX` | `str`     | local time; tz=`Europe/Amsterdam`    | 0:05:00                         | electricity meter reading                  | `DSMR-P1-gateway`            | 0-0:1.0.0.255                                                | `eMeterReadingTimestamp`   | YYMMDDhhmX |                                  |                                            |                              |                                                                                                                                                                 |                            |                                                                       |
| `g_timestamp__YYMMDDhhmX` | `str`     | local time; tz=`Europe/Amsterdam`                                                                                                                                                                                                                                                 | 0:05:00 / 1:00:00 [^1]                         | gas meter reading                          | `DSMR-P1-gateway`            | 0-0:1.0.0.255                                                                                                                                                   | `gMeterReadingTimestamp`   | YYMMDDhhmX                                                            |
| `g_use_cum__m3`           | `float64` | m<sup>3</sup>                                                                                                                                                                                                                                              | 0:05:00                          | gas meter reading                          | `DSMR-P1-gateway`            | 0-n:24.2.1.255                                                                                                                                                  | `gMeterReadingSupply`      | %.3f                                                                  |

[^1]: Smart meters with DSMR ≥ 5.0 report meter readings every 5 minutes, smart meters with DSMR \< 5.0 report gas meter readings only every hour. 
### Weather measurements

Weather data was collected and geospatially interpolated using [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) from the Royal Netherlands Meteorological Institute ([KNMI](https://www.knmi.nl/over-het-knmi/about)), based on average hourly values. 

For all homes, we used the same location for geospatial interpolation of weather data:
[`lat, lon = 52.50655, 6.09961`](https://www.openstreetmap.org/?mlat=52.50655&mlon=6.09961#map=17/52.50655/6.09961), the center of the Assendorp neighbourhood in Zwolle, the Netherlands. Average values were converted from the source units to the units as indicated in the table below. 


| Index/Column | Property        | Type        | Unit            | Measurement interval \[h:mm:ss\] | Description                                                  | Source | [Source property](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens) | [Source value format](https://en.wikipedia.org/wiki/Printf_format_string) | Source unit                                            |
| ------------ | --------------- | ----------- | --------------- | -------------------------------- | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------ |
| index        | `timestamp`     | `Timestamp` |                 |                                  | start of the measurement interval | KNMI   | `YYYMMDD`, `H`                                               |                                                              | H=1: 0:00:00 - 0:59:59; H=24: 23:00:00 - 23:59:59; |
| column       | `temp_out__degC` | `float32`   | °C              | 1:00:00                          | outdoor temperature                                          | KNMI   | ` T`                                                         | %d                                                           | 0.1&nbsp;°C                                            |
| column       | `wind__m_s_1`    | `float32`   | m/s             | 1:00:00                          | wind speed                                                   | KNMI   | ` FH`                                                        | %d                                                           | 0.1&nbsp;m/s                                           |
| column       | `ghi__W_m_2`     | `float32`   | W/m<sup>2</sup> | 1:00:00                          | global horizontal irradiance                                 | KNMI   | ` Q`                                                         | %d                                                           | J/(h·cm<sup>2</sup>)                                       |

### Preprocessed data 

Preprocessing of measurements in the measurement database was done using [get_preprocessed_homes_data()](https://github.com/energietransitie/twomes-twutility-inverse-grey-box-analysis/blob/main/data/extractor.py). Preprocessing steps include:
- if available, using timestamps based on `eMeterReadingTimestamp` or `gMeterReadingTimestamp` for smart meter reading measurements, instead of the timestamp obtained from the [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware);
- removal of duplicate measurements;
- calculation of derived properties as a combination of other properties, as indicated in the column `Calculation` in the table below;
- removal of absolute outliers, i.e measurement values smaller than the value in the column `Min` or larger than the value in the column `Max` in the table below;
- removal of statistic outliers, i.e. measuremnt values with an absolute [z-score](https://en.wikipedia.org/wiki/Standard_score) higer than the value indicated in the `Sigma` column in he table below;
- interpolation of measurements to intervals of 15 minutes (no interpolation between measurements that were 60 minutes apart or more);
- All column values represent the average during the interval that starts at the timestamp indicated. 

| **Index/  Column** | **Name**       | **Type**        | **Unit**        | **Description**                                              | **Calculation**  | Min | Max | Sigma |
| ---------------- | ------------ | ----------- | --------------- | ------------------------------------------------------------ | --------------- | --------------: | --------------: | --------------: |
| index            | `id`         | `Int16`     |                 | unique code of the home                                      |                 | 800000 | 899999 |                 |
| index            | `timestamp`  | `Timestamp` |                 | start of the interpolated interval (timezone aware) |                 |                 |                 |                 |
| column           | `temp_out__degC` | `float32`   | °C              | outdoor temperature                                          |                 | -28 | 40 |                 |
| column           | `wind__m_s_1` | `float32`   | m/s             | wind speed                                                   |                 | 0 | 35 |                 |
| column           | `ghi__W_m_2`  | `Int16`     | W/m<sup>2</sup> | global horizontal irradiance                                |                 | 0 | 1000 |                 |
| column           | `temp_in__degC`  | `float32`   | °C              | indoor temperature                                           |                 | 0 | 40 | 3 |
| column           | `temp_set__degC` | `float32`   | °C              | thermostat setpoint temperature                              |                 | 0 | 40 |                 |
| column           | `g_use__W`  | `Int16`     | W               | natural gas power used (superior calorific value)            | Δ`g_use_cum__m3` · `h_sup__J_m_3` / Δ`timestamp`  [^2] | 0 | 1e5 |  |
| column           | `e_use__W`    | `Int16`     | W               | electrical power obtained from the grid                      | (Δ`e_use_hi_cum__m3`+ Δ`e_use_lo_cum__m3`) · `J_kWh_1` / Δ`timestamp` [^3] | 0 | 2e4 |  |
| column           | `e_ret__W`    | `Int16`     | W               | electrical power returned to the grid                        | (Δ`e_ret_hi_cum__m3`+ Δ`e_ret_lo_cum__m3`) · `J_kWh_1` / Δ`timestamp` [^3] | 0 | 2e4 |  |

[^2]: `h_sup__J_m_3 = 35.17e6` J/m<sup>3</sup><br>Conversion factor for the [superior calorific value of natural gas from the Groningen field](https://en.wikipedia.org/wiki/Groningen_gas_field#Properties_Groningen_gas).

[^3]: `J_kWh_1 = 3.6e6` J/kWh<br>Conversion factor from kWh to J.

## Status
Dataset is: _collected_, _anonimization-in-progress_

## License
This data is made available under the [CC BY 4.0](./LICENSE.md) by the [Research group Energy Transition, Windesheim University of Applied Sciences](https://windesheim.nl/energietransitie) 

## Credits
* Henri ter Hofte · [@henriterhofte](https://github.com/henriterhofte) · Twitter [@HeNRGi](https://twitter.com/HeNRGi)
* [Stichting 50 Tinten Groen Assendorp](https://50tintengroenassendorp.nl/)

Thanks go to those who are the ultimate source of this dataset:
* all anonymous subjects who volunteered to make their measurement data available

We use and gratefully aknowlegde the efforts of the makers of the following source code and libraries:
* [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather), by [@stephanpcpeters](https://github.com/stephanpcpeters), licensed under [an MIT-style licence](https://raw.githubusercontent.com/stephanpcpeters/HourlyHistoricWeather/master/historicdutchweather/LICENSE)
