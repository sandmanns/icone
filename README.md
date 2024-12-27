# ICONE: Infectious diseases COntact NEtworks
Contact networks for the interactive analysis of infection chains in hospitals

<p align="center">
    <img height="600" src="https://uni-muenster.sciebo.de/s/9tGJYTDAhc13som/download">
</p>


## Requirements
To run ICONE, you need R (Version 4.1.0 or higher) and R Shiny.

##  Installation
To install ICONE, just download the repository. All required packages will be installed, all functions loaded automatically by the help of the global.R script.

## Running ICONE
ICONE is available as an R Shiny GUI. To run the software: 1) navigate to the folder in which the following files are stored: `global.R`, `ui.R`, `server.R`, `www/UKM.png` and `www/white.png`. 2) Execute `Run App`.


## Examplary use of ICONE

## Input
To execute any analyses with ICONE, in input file has to be 1) read in and 2) configured.

### Read in file

* Clinics:
    * `Upload data` Select a tabular file for upload, containing information on admission and discharge of cases from clinics.
    * `Separator` Select the field separator used in the input file (one of: Comma, Semincolon, Tab). 
* Units:
    * `Upload data` Select a tabular file for upload, containing information on admission and discharge of cases from units.
    * `Separator` Select the field separator used in the input file (one of: Comma, Semincolon, Tab). 

Note: at least one file has to be uploaded. A second file, e.g. providing more fine-granular information on transfer of cases between units, so, within one clinic, can be uploaded.


### Configure input

* `Select column containing information on...`
  * `...case number` Name of the column containing information on the considered cases (case ID).
  * `...clinic/unit` Name of the column containing information on the clinic/unit.
  * `...admission` Name of the column containing information on the admission of the case to the clinic/unit (format requirement: YYYY-MM-DD hh:mm:ss).
  * `...discharge` Name of the column containing information on the discarge of the case from the clinic/unit (format requirement: YYYY-MM-DD hh:mm:ss).
  * `...main diagnosis` Name of the column containing information on the main diagnosis (ICD code).

      

### Example

Select `Load demo data`to read in the simulated exemplary input files `Clinic_V2.txt` and `Unit_V2.txt`. Both files are available in the www folder and can, as an alternative, be uploaded manually.

In case you are reading in the demo data automatically, the colums to configure the input are already defined correctly by default ('CaseID' for `case number`, 'Clinic' for `clinic`, 'Unit' for `unit`, 'Admission' for `admission`, 'Discharge' for `discharge` and 'MainDiagnosis' for `main diagnosis`).


## Contact analysis 

You can switch between an analysis per clinic and per unit.

### Case selected
For every analysis, one clinic (or unit) and a case in this clinic is in focus. The case represents the center of an outbreak. Optionally, only selected ICD codes or chapters my be considered.


### Selected observation period
For the analysis, an observation period is selected.

The earliest date for the `Start of observation period` is the day of the case's admission to the clinic in focus. The latest date for the `Start of the observation period` is the case's discharge from the clinic in focus.

The earliest date for the `End of observation period` is the day selected for the start of the observation period. The latest date for the `End of the observation period` is the last day for which information on the clinic in focus is available in the uploaded input file.

Please consider that for a long observation period, the calculations may - dependent on the computer's performance - take up to 1 minute.

### Contact analysis
For the contact analysis, 3 parameters have to be defined:

* Latency period: The time from the infection of a case until being infectious his-/herself. If it is valid to assume that the case in focuse is already infectious, starting with the observation period, `Latency period also for case in focus?` should be set to inactive. Otherwise, this parameter should be activated.
* Minimum exposition time: The time that two cases have to be in contact to transfer an infection.
* Infectious phase: The time for which a case is infectious.

* Maximum length of infection chain: for the analysis, the length of the infection change to consider can be defined. A value between 1 and 10, or `unlimited` may be selected (default: 1).


## Results
The primary result of the analysis with ICONE is an interactive contact network (R package 'networkD3').

### Basic analysis

<b> Visualization: </b>

In the center of the network, the case in focus is visualized (black, blue border).

Every contact that has led to an infection, is connected to the case by a gray edge. The edge's thickness correlates with the length of the exposition time. The node's size correlates with the length of the stay on the selected clinic/unit. 

If a chain length >1 is selected, for 1st level contacts, further contacts are analyzed that have led to an assumed transmission of the infection, matching the previously defined requirements (latency period, minimum exposition time, infectious phase); continued for higher levels. The level of the infection/chain length is color-coded. A lighter hue signales a higher level.

If a node is displayed with a black border, this indicates that the case visited at least one more clinic/unit during his/her infectious phase. Consequently, there is a risk for expansion of the infection to other clinics.

If the case in focus has visited one (or more) further clinics/units while being infectious, for every additional clinic, another black node is displayed. This node is connected to the central node by a black endge. The additional nodes are differentiated by color-coding (first additional clinic: orange border; second: green, third: red etc.).

<b> Pop-up window: </b>

For every node, detailed information can be obtained. By clicking on the node, a window pops up. The following information is provided:

* Clinic
* Case
* Main diagnosis
* Infection
    * At (date + time)
	* By (case number, level of infection)
* Infectious in clinic
    * Form - until (date + time)
	* Length of stay
	* Cases infected
* Further clinics visited while being infectious


<b> General information: </b>

On top of the visualization, basic information is summarized:

* Configuraiton of the analysis
    * Clinic
	* ICD filter
	* Case in focus
	* Observation period
	* Latency period
	* Minimum exposition time
	* Infectious phase
	* Maximum length of infection chain
* Additional information
    * Assumed infected cases in clinic
	* Infection risk for x/y further clinics: enumeration of the clinics + number of transfers, sorted by the number of transfers (indicator for a higher risk for expansion of the infection)


<b> Example: </b>

Please select:

* Clinics
* Select case
    * Clinic in focus: Clinic 1
    * Filter main diagnosis by ICD code: no
    * Case in focus: 1000001
* Select observation period
    * Start of observation period: 2023-03-22
    * End of observation period: 2023-04-21
* Contact analysis
    * Latency period: 2 Stunden
    * Latency period also for case in focus: no
    * Minimum exposition time: 1 hour
    * Infectious phase: 9 days
    * Maximum length of infection chain: 3


### Extended analysis: + further clinics visited (case: focus, length infection chain: basic analysis)
<b> Visualization: </b>

If the case in focus visits at least one more clinic while being infectious, the analysis may be extended to these clinics. To do so, the basic analysis' graph is extended as follows:

For every black node with non-blue border, contacts on all other clinics visisted during the infectious phase are determined. Per unit, a unique color is selected. The length of the infection chain is the same for all clinics visited by the case in focus, corresponding to the basic analysis' definition.

For every new contact on the other clinics, it is again determined whether this patient has visisted one or more clinics him-/herselv (black border).


<b> Pop-up window: </b>

Analoguously.

<b> General information: </b>

Analoguously. 

Assumed infected case in clinic and infection risk for x/y further clinics updated correspondingly (number of transfers).


<b> Example: </b>

Please select:

* Clinics
* Select case
    * Clinic in focus: Clinic 6
    * Filter main diagnosis by ICD code: no
    * Case in focus: 1000001
* Select observation period
    * Start of observation period: 2023-03-12
    * End of observation period: 2023-04-11
* Contact analysis
    * Latency period: 2 Stunden
    * Latency period also for case in focus: no
    * Minimum exposition time: 1 hour
    * Infectious phase: 13 days
    * Maximum length of infection chain: 2
	

Extend the basic analysis for this example. 


### Extend analysis: + further clinics visited (case: all, length infection chain: 1)
<b> Visualization: </b>

If at least one case has visited at least one more clinic while being infectious, the analysis may be extended to these clinics. To do so, the basic analysis' graph is extended as follows:

For every node with black border, contacts on all other clinics visisted during the infectious phase are determined. The same clinics that have e.g. been visited by different cases, are marked by the same color-code. 

For every new contact on the other clinics, it is again determined whether this patient has visisted one or more clinics him-/herselv (black border).

To ensure clear visualization of the results, the length of the infection chain for these further clinics is limited to 1. If new clinics at risk are identified by this analysis, a new analysis with this new clinic in focus is advised.


<b> Pop-up window: </b>

Analoguously.

<b> Further information: </b>

Analoguously. 

Assumed infected case in clinic and infection risk for x/y further clinics updated correspondingly (number of infections + number of transfers).


<b> Example: </b>

Extend the basic analysis for the example described above. 
