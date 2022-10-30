# Dataset Assendorp 2021 - Digital Twins for the heating transition
Dataset containing anonimized energy and temperature data from homes in the Assendorp neighbourhood in Zwolle, the Netherlands

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

*The dataset is currently in review to ensure it meet our standards for anonimization.*

**Note**: [Git LFS](https://git-lfs.github.com/) is required to clone big CSV files

## Recruitment 

Subjects were recruitedfrom the municipality of Zwolle during autumn 2021. Recruitment was primarily targeted at the Assendorp neighbourhood, via [50 Tinten Groen Assendorp](https://50tintengroenassendorp.nl/), e.g. via a [news message](https://50tintengroenassendorp.nl/40-13-september-2021-warmtewachters-gezocht/). In November 2021, additional subjects were recruited amongst colleagues and students of Windesheim University of Applied Sciences, e.g. via an [internal message](https://liveadminwindesheim.sharepoint.com/sites/rvd-Domein-Techniek/Lists/Announcements/DispForm.aspx?ID=181). 

Subjects could volunteer to participate and give informed consent by filling out this [generic online recruitment survey](https://edu.nl/yyhpt) ([this survey is also available in XML-format](https://www.energietransitiewindesheim.nl/assendorp2021/recruitment/Survey_Twomes_Bluetooth-v10.qsf)). Subjects that satified all [inclusion criteria](#inclusion-criteria) were also asked to fill out this [online survey to provide their static Bluetooth MAC addressto us](https://edu.nl/wfnc4) ([this survey is also available in XML-format](https://energietransitiewindesheim.nl/assendorp2021/recruitment/Survey_Twomes_Bluetooth-v10.qsf)) and were asked to forward this latter survey to the other members of the household with a smartphone.   

## Inclusion criteria

Inclusion criteria were:
* the home address lies in the muncipality of Zwolle, the Netherlands;
* the home is equipped with an internet connection and wireless internet (Wi-Fi);
* the home is heated by a gas-fired heating boiler that is controlled by a termostat and is not predominantly heated via other means;
* a smart energy meter is installed in the home;
* at least one of the occupants has an Android/iOS smartphone.

## Data management

We documented our [Data Management Plan](https://edu.nl/38wq4) online. The privacy policy (in Dutch) is available online as well in a layered structure: [short summary](https://www.energietransitiewindesheim.nl/assendorp2021/privacy.html), [summary](https://www.energietransitiewindesheim.nl/assendorp2021/privacy-summary.html) and [full version](https://www.energietransitiewindesheim.nl/assendorp2021/privacy-full.html). 

## Data

In the sections below, the data pre-processing and data formats used in the data files will be described.

### Data pre-processing

Preprocessing of measurements in the measurement database was doen using [the get_preprocessed_homes_data function in the extractor.py file of the twomes-twutility-inverse-grey-box-analysis repository](https://github.com/energietransitie/twomes-twutility-inverse-grey-box-analysis/blob/main/data/extractor.py). 

Steps include:
- data retrieval from the measurement server for selected homes, properties and date range
- for smart meter measurement values, conversion of meter readings to energy usage;
- absolute outlier removal: removing measurement data that is clearly a measurement error:
  - indoor temperatures below 0 °C and above 40 °C;
  - setpoint temperatures below 0 °C and above 45 °C;
- statistic outlier removal: removing temperature data that are more than 3 standard deviations away from the mean of the measurements;
- upsampling of measurements to 5 minutes;
- interpolation of measurements to intervals of 15 minutes;
  - we did not interpolate across valid measurements that were 60 minutes apart or more.
  - we aggregated measurements over the interval as indicated in the table in the section [Interpolated and preprocessed data](#interpolated-and-preprocessed-data), below;

We used the following converstation factors:
* 35,17 MJ/m<sup>3</sup> [superior calorific value of natural gas from the Groningen field](https://en.wikipedia.org/wiki/Groningen_gas_field#Properties_Groningen_gas) to convert `gMeterReadingSupply` values to `gas_sup_avg_W` values
* timezone [Europe/Amsterdam](https://en.wikipedia.org/wiki/Time_in_the_Netherlands) to convert `unix_time` to `timestamp_ISO8601`

### Date and time information

All timestamps were measured in [Unix time](https://en.wikipedia.org/wiki/Unix_time) format, using device clocks regularly synchronized via NTP with the correct UTC time. Timestamps of the start of measurement intervals of interpolated and pre-processed measurement data is not only represented as represented as [Unix time](https://en.wikipedia.org/wiki/Unix_time), but also as a [fully qualified ISO 8601 timestamp](https://en.wikipedia.org/wiki/ISO_8601) time stamp in the format _YYYY-MM-DDThh:mm:ss±hh:mm_, using the timezone [Europe/Amsterdam](https://en.wikipedia.org/wiki/Time_in_the_Netherlands).

For smart meter measurements, if available and sane, the time information  in the `eMeterReadingTimestamp` property or `gMeterReadingTimestamp` was used as timestamp of the measurement.

Setting the local device clock to the proper UTC time via NTP was one of the first steps performed by the measurement devices after they were connected to the internet via the home Wi-Fi network of a subject. Each measurement device synchronized its device clock via NTP every 6 hours. Uploads of measurement data (which could contain more than one measurement) were timestamped both by the measurement device according to the local device clock and by the server. We did not yet check for deviations between the last device timestamp of a measurement upload and the upload timestamp at the server.

### Homes 

TODO: describe

### Weather data

Weather data was collected and geospatially interpolated using [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) from the Royal Netherlands Meteorological Institute (KNMI). Below is a table that documents the weather properties that we retrieved from KNMI.

| Repository                                                                        | Symbol  | Property.Unit | [Value format](https://en.wikipedia.org/wiki/Printf_format_string) | Measurement interval \[h:mm:ss\] | column name interpolated data          | Description                   |
| --------------------------------------------------------------------------------- | ------- | ------------- | ------------------------------------------------------------------ | -------------------------------- | -------------------------------------- | ----------------------------- |
| [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) | ` T`  | °C            | %f                                                                 | 01:00:00                         | outdoor\_temp\_degC                    | outdoor temperature           |
| [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) | ` FH` | m/s           | %f                                                                 | 01:00:00                         | windspeed\_m\_per\_s                   | wind speed                    |
| [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) | ` Q`  | J/(h\*cm<sup>2</sup>)   | %f                                                                 | 01:00:00                         | hor\_irradiation\_J\_per\_h\_per\_cm^2 | global horizontal irradiation |

For all homes, we used the same location for geospatial interpolation of weather data using code from the [HourlyHistoricWeather](https://github.com/stephanpcpeters/HourlyHistoricWeather) repository:
[`lat, lon = 52.50655, 6.09961`](https://www.openstreetmap.org/?mlat=52.50655&mlon=6.09961#map=17/52.50655/6.09961).

### Measurement Devices 

We used the following measurement device types to collect data. Some devices consisted of a main device and one or two satellite devices.

| DeviceType.name             | Category                                                | Main device repo                                                                                           | Sattelite device 2 repo                                                                          | Sattelite device 2 repo                                                                              |
| -------------------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| `OpenTherm-Monitor`         | comfort + installation + occupancy                      | [twomes-opentherm-monitor-firmware](https://github.com/energietransitie/twomes-opentherm-monitor-firmware) |                                                                                                  |                                                                                                      |
| `DSMR-P1-gateway`           | energy                                                  | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               |                                                                                                  |                                                                                                      |
| `DSMR-P1-gateway-Tin`       | energy + comfort                                        | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) |                                                                                                      |
| `DSMR-P1-gateway-TinTsTr`   | energy + comfort + installation                         | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)             | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) | [twomes-boiler-monitor-firmware](https://github.com/energietransitie/twomes-boiler-monitor-firmware) |
| `DSMR-P1-gateway-TinTsTrCO2`| energy + comfort + installation + occupancy/ventilation | [twomes-p1-gateway-firmware](https://github.com/energietransitie/twomes-p1-gateway-firmware)               | [twomes-room-monitor-firmware](https://github.com/energietransitie/twomes-room-monitor-firmware) | [twomes-boiler-monitor-firmware](https://github.com/energietransitie/twomes-boiler-monitor-firmware) |

### Measured Properties 

Below is a table that describes all properties that were measured by various measurement devices, including the name of the column in the [interpolated measurements](#interpolated-measurements).

| DeviceType.name                 | Category              | Measurement interval \[h:mm:ss\] | Property.name                 | Property.Unit | [Value format](https://en.wikipedia.org/wiki/Printf_format_string) | used for interpolated data column | Description                                | Sensor                                                                      | OBIS-reference | [OpenTherm ID](https://github.com/Nodo-Domotica/OpenTherm-GateWay/blob/master/OT-Protocol-Specification-v2-2.pdf) | [OpenTherm part](https://github.com/Nodo-Domotica/OpenTherm-GateWay/blob/master/OT-Protocol-Specification-v2-2.pdf) |
| ------------------------------- | --------------------- | -------------------------------- | ----------------------------- | ------------- | ------------------------------------------------------------------ | ----------------------------- | ------------------------------------------ | --------------------------------------------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `DSMR-P1-gateway-TinTsTrCO2`| occupancy/ventilation | 00:05:00                         | `CO2concentration`        | ppm           | %d                                                                 |                               | CO₂ concentration                          | [SCD41](https://sensirion.com/products/catalog/SCD41/)                      |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway-TinTsTrCO2`| occupancy/ventilation | 00:05:00                         | `humidity`                | %RH           | %d                                                                 |                               | relative humidity                          | [SCD41](https://sensirion.com/products/catalog/SCD41/)                      |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway-TinTsTrCO2`| comfort               | 00:05:00                         | `roomTempCO2`             | °C            | %.1f                                                               | `T_in_avg_C`       | air temperature                            | [SCD41](https://sensirion.com/products/catalog/SCD41/)                      |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway-TinTsTr`   | comfort               | 00:05:00                         | `roomTemp`                | °C            | %.1f                                                               | `T_in_avg_C`            | air temperature                            | [Si7051](https://www.silabs.com/sensors/temperature/si705x/device.si7051)   |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway-TinTsTr`   | installation          | 00:00:10                         | `boilerTemp1`             | °C            | %.1f                                                               |                               | temperature of hydronic supply/return pipe | [DS18B20](https://www.maximintegrated.com/en/products/sensors/DS18B20.html) |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway-TinTsTr`   | installation          | 00:00:10                         | `boilerTemp2`             | °C            | %.1f                                                               |                               | temperature of hydronic supply/return pipe | [DS18B20](https://www.maximintegrated.com/en/products/sensors/DS18B20.html) |                |                                                                                                                   |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:10:00                         | `heartbeat`               | \[-\]         | %d                                                                 |                               | measurement system heartbeat               | (digital)                                                                   |                |                                                                                                                   |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `isBoilerFlameOn`         | boolean       | 0/1                                                                |                               | STATUS /Flame status                       | (digital)                                                                   |                | 0                                                                                                                 | LB/3                                                                                                                |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `isCentralHeatingModeOn`  | boolean       | 0/1                                                                |                               | STATUS/CH mode                             | (digital)                                                                   |                | 0                                                                                                                 | LB/1                                                                                                                |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `isDomesticHotWaterModeOn`| boolean       | 0/1                                                                |                               | STATUS/DHW mode                            | (digital)                                                                   |                | 0                                                                                                                 | LB/2                                                                                                                |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `maxModulationLevel`      | %             | %d                                                                 |                               | CAPACITY SETTING                           | (digital)                                                                   |                | 14                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `maxBoilerCap`            | kW            | %d                                                                 |                               | MAX CAPACITY                               | (digital)                                                                   |                | 15                                                                                                                | HB                                                                                                                  |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `minModulationLevel`      | %             | %d                                                                 |                               | MIN-MOD-LEVEL                              | (digital)                                                                   |                | 15                                                                                                                | LB                                                                                                                  |
| `OpenTherm-Monitor`         | comfort               | 00:05:00                         | `roomSetpointTemp`        | °C            | %.2f                                                               | `T_set_first_C`  | ROOM SETPOINT                              | (digital)                                                                   |                | 16                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:00:30                         | `relativeModulationLevel` | %             | %d                                                                 |                               | RELATIVE MODULATION LEVEL                  | (digital)                                                                   |                | 17                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | comfort               | 00:05:00                         | `roomTemp`                | °C            | %.2f                                                               | `T_in_avg_C`            | ROOM TEMPERATURE                           | (digital)                                                                   |                | 24                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:05:00                         | `boilerMaxSupplyTemp`     | °C            | %.2f                                                               |                               | MAX CH WATER SETPOINT                      | (digital)                                                                   |                | 57                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:00:10                         | `boilerSupplyTemp`        | °C            | %.2f                                                               |                               | BOILER WATER TEMP.                         | (digital)                                                                   |                | 25                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | installation          | 00:00:10                         | `boilerReturnTemp`        | °C            | %.2f                                                               |                               | RETURN WATER TEMPERATURE                   | (digital)                                                                   |                | 28                                                                                                                |                                                                                                                     |
| `OpenTherm-Monitor`         | occupancy/ventilation | 01:00:00                         | `listRSSI`                | \[dBm\]       | %d                                                                 |                               | Bluetooth presence                         | (digital)                                                                   |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:10:00                         | `heartbeat`               | \[-\]         | %d                                                                 |                               | measurement system heartbeat               | (digital)                                                                   |                |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `eMeterReadingSupplyLow`  | kWh           | %.3f                                                               | `e_used_avg_W`             | electricity meter reading                  | (digital)                                                                   | 1-0:1.8.1      |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `eMeterReadingSupplyHigh` | kWh           | %.3f                                                               | `e_used_avg_W`          | electricity meter reading                  | (digital)                                                                   | 1-0:1.8.2      |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `eMeterReadingReturnLow`  | kWh           | %.3f                                                               | `e_returned_avg_W`         | electricity meter reading                  | (digital)                                                                   | 1-0:2.8.1      |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `eMeterReadingReturnHigh` | kWh           | %.3f                                                               | `e_returned_avg_W`      | electricity meter reading                  | (digital)                                                                   | 1-0:2.8.2      |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `eMeterReadingTimestamp`  | local time    | YYMMDDhhmX                                                         | `timestamp`                     | electricity meter reading                  | (digital)                                                                   | 0-0:1.0.0.255  |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `gMeterReadingTimestamp`  | local time    | YYMMDDhhmX                                                         | `timestamp`                     | gas meter reading                          | (digital)                                                                   | 0-0:1.0.0.255  |                                                                                                                   |                                                                                                                     |
| `DSMR-P1-gateway`           | energy                | 00:05:00                         | `gMeterReadingSupply`     | m<sup>3</sup>           | %.3f                                                               | `gas_sup_avg_W`                      | gas meter reading                          | (digital)                                                                   | 0-n:24.2.1.255 |                                                                                                                   |                                                                                                                     |

### Measurements 

| **Column index** | **Column name**  | **Description**                                                                                            |
|------------------|------------------|------------------------------------------------------------------------------------------------------------|
| 0                | `#`              | record number                                                                                              |
| 1                | `home_id`        | subject code                                                                                               |
| 3                | `unix_time`      | start date and time of the measurement represented as [Unix time](https://en.wikipedia.org/wiki/Unix_time) |
| 4                | `property_name`  | property name of the measurement                                                                           |
| 5                | `value`          | value of the measurement                                                                                   |
| 6                | `unit`           | unit of the measurement value                                                                              |
| 7                | `measurement_id` | unique id of the measurement                                                                               |

### Interpolated and preprocessed data 

| **Column index** | **Column name**            | **Description**                                   | **Unit** | **[Value format](https://en.wikipedia.org/wiki/Printf_format_string)**               | **Interval aggregation** | **Calculation**                                          |
|------------------|----------------------------|---------------------------------------------------|----------|--------------------------|--------------------------|----------------------------------------------------------|
| 0                | `#`                    | record number                                      |          |                          | -                        |                                                          |
| 1                | `home_id`                    | subject code                                      |          |                          | -                        |                                                          |
| 2                | `timestamp_ISO8601`                  | start date and time of interval (timezone aware)  |          | [YYYY-MM-DDThh:mm:ss±hh:mm](https://en.wikipedia.org/wiki/ISO_8601) | start                    |                                                          |
| 3                | `unix_time`                     | start date and time of interval represented as [Unix time](https://en.wikipedia.org/wiki/Unix_time)                                         |          | %d                       | start                    |                                                          |
| 4                | `T_out_avg_C`                | outdoor temperature                               | °C       | %.2f                     | average                  |                                                          |
| 5                | `wind_avg_m_p_s`             | wind speed                                        | m/s      | %.2f                     | average                  |                                                          |
| 6                | `irradiation_hor_avg_W_p_m2` | global horizontal irradiation                     | W/m<sup>2</sup>     | %d                       | average                  |                                                          |
| 7               | `T_out_e_avg_C`              | effective outdoor temperature                     | °C       | %.2f                     | average                  | `T_out_avg_C` - 2/3 * `wind_avg_m_p_s`                       |
| 8               | `T_in_avg_C`                 | indoor temperature                                | °C       | %.2f                     | average                  |                                                          |
| 9               | `T_set_first_C`              | thermostat setpoint temperature                   | °C       | %.2f                     | latest valid at start    |                                                          |
| 10               | `interval_s`                 | duration of interval                              | s        | %d                       | sum                      |                                                          |
| 11               | `gas_sup_avg_W`              | natural gas power used (superior calorific value) | W        | %d                       | average                  |                                                          |
| 12               | `e_used_avg_W`               | electrical power obtained from the grid           | W        | %d                       | average                  |                                                          |
| 13               | `e_returned_avg_W`           | electrical power returned to the grid             | W        | %d                       | average                  |                                                          |
| 14               | `e_remaining_heat_avg_W`     | net electrical power obtained from the grid       | W        | %d                       | average                  | `e_used_avg_W` - `e_returned_avg_W`                          |

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
