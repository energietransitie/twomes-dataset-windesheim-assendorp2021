# twomes-dataset-assendorp2021
Dataset containing anonimized energy and temperature data from homes in the Assendorp neighbourhood in Zwolle, the Netherlands

## Table of contents
* [General info](#general-info)
* [Data pre-processing](#data-pre-processing)
* [Date and time information](#date-and-time-information) 
* [Data formwat](#data-format) 
* [Status](#status)
* [License](#license)
* [Credits](#credits)

## General info
This repository will contain a curated dataset comprising time series data we obtained in the Twomes research project from homes Assendorp neighbourhood in Zwolle, the Netherlands during the heating season of 2021-2022.

**Note**: [Git LFS](https://git-lfs.github.com/) is required to clone big CSV files

## Data pre-processing

A future release of this section described per device/system how the data was obtained and pre-processed. <br>
Refer to the [data format section](#data-format) for a detailed description of the data format.


## Date and time information

Date and time information was equalized to a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) time stamp in the format _YYYY-MM-DDThh:mm:ss±hhmm_. For convenience the date, time and UTC offset were also added as separate columns.

All timestampes were collected with measurement devices connected to the internet. Setting the local device clock to the proper UTC time via NTP was one of the first steps performed by the measurement devices after they were connected to the internet via the home Wi-Fi network of a subject. Each measurement device synchronized its device clock via NTP every 6 hours. Measurement devices timestamped measurement with Unix time according to the local device clock. Uploads of measurement data (which could contain more than one measurement) were timestamped both by the measurement device according to the local device clock and by the server. Before exporting to the csv files in this repository, all timestampes were converted to timezone-aware timestamps using the Europe/Amsterdam timezone.

## Data format

In this section the data format will be described.<br>

### Homes 

### Weather 

### Properties 

### Devices 

### Measurements 

## Status
Dataset is: _collected_, _anonimization-in-progress_

## License
This data is available under the [CC BY 4.0](./LICENSE.md), by the [Research group Energy Transition, Windesheim University of Applied Sciences](https://windesheim.nl/energietransitie) 

## Credits
This dataset is a collaborative effort of:
* Henri ter Hofte · [@henriterhofte](https://github.com/henriterhofte) · Twitter [@HeNRGi](https://twitter.com/HeNRGi)
* <contributor name 2> · [@Github_handle_2](https://github.com/<github_handle_2>) · Twitter [@Twitter_handle_2](https://twitter.com/<twitter_handle_2>)
* <contributor name 3> · [@Github_handle_3](https://github.com/<github_handle_3>) · Twitter [@Twitter_handle_3](https://twitter.com/<twitter_handle_3>)
* etc. 
* 
Thanks also go to:
* all subjects who volunteered to make their measurement data available
* [Stichting 50 Tinten Groen Assendorp](https://50tintengroenassendorp.nl/)
