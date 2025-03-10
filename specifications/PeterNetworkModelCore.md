
# Peter Halling network data model for applied biocatalysis - file focussing on core Objects, much referenced elsewhere

This file has cross-references to Objects in other files in order to make up the whole model. The whole model is a draft and does not yet include all the attributes we have identified, so it would need extended. The aim is to show how attributes can be grouped to minimise repetition - so that each attribute appears only once whenever possible. Thus details about description, requirements, validation etc can be specified (and subsequently coded) just in one place for each group of attributes. This draft is obviously based on the data model we have worked on extensively and published, and also takes inspiration from ideas from others, particularly Carsten Kettner and Jan Range.

### BiocatalysisReactionSet

Collects a set of biocatalysis reactions carried out as (part of) a study. Each will be essentially a batch or fed-batch reaction, perhaps with continuous gas supply. Typically most of the conditions will be similar for all the reactions in the set, but there will be some differences in one or more attributes, e.g. temperature, pH, type of biocatalyst, starting materials used, concentrations in the reaction mixture. Some reactions will probably be replicates. Results from the whole set may be summarised by fitted values, for example kinetic parameters.

- Reactions
  - Type: BiocatalysisReaction
  - description: Details of one reaction
  - Multiple: True
- ResultsAnalyis
  - Type: string
  - description: Details of analysis applied to (part of) the whole set. Just a placeholder at present, should later refer to sub-table include details of any models used, fitting approach, fitted parameters (e.g. Km, V, E etc). Could also be graphs presenting dependencies on varied conditions.

### BiocatalysisReaction

Collects details of one reaction, essentially a batch or fed-batch, perhaps with continuous gas supply. 

- Conditions
  - Type: BatchIncubation
  - description : Details of conditions for this reaction
- WhatIsMeasured
  - Type: MeasuredSpecies
  - description: Identifies the species measured to monitor the progress of the reaction, and some information about the methods applied to do so. If more than one species are measured, should be assigned identifiers so can be referred to in time point data. In the case of chiral materials, probably want to allow recording of total concentration and enantiomeric excess, instead of just two concentrations.
  - Multiple: True
- ProgressData
  - Type: TimePoint
  - description: One or more data points showing the reaction progress, i.e. time values and corresponding concentrations of measured species
  - Multiple: True
- InitialRate
  - Type: float
  - description: An estimate of initial rate. If the reaction is monitored continuously (e.g. by spectrophotometry) the initial rate will be extracted fairly directly. In cases of discontinuous data, initial rate might be estimated by analysis of a series of progress data points. Could expand this to a separate Object, including details of method used to estimate initial rate – but probably this goes beyond what would normally be considered necessary. NOTE: In this and many other cases the Type float is used to indicate a quantity with units. In our current public version this is usually handled by two coupled attributes for the numerical value and the units (as a string). Better is to define a Type that links the value and the units. But for the present I use just on attribute with type float.

### MeasuredSpecies
Collects the identity of a species measured in order to monitor the reaction, together with some information on the analytical method used

- TheSpecies
  - Type: SmallCompound, Macromolecule
  - description: The identity of the species measured
- MeasurementMethod
  - Type: string
  - description: The method used to measure its concentration, e.g. spectrophotometry, HPLC, GC, NMR. Better change this to an enumerated list.
- MethodDetails
  - Type: string
  - description: Any necessary details of how the method is applied, including standardisation

### TimePoint

Results of analysis at a single time point during a reaction

- ReactionTime
  - Type: float
  - description: The elapsed time from the start of the reaction
- MeasuredConcentrations
  - Type: float
  - description: The concentration(s) measured at that time point. If more than one species are measured during the reaction, need to use identifiers so each concentration is linked to correct species. Need to allow for possibility that at each time point not every species may be measured.
  - Multiple: True


### BatchIncubation

Describes an operation in which a mixture is prepared and then held under defined conditions for a period of time, during which a reaction or similar process may occur. This might be a biocatalytic reaction, but can also be a step in chemical modification, immobilisation or some other process as part of biocatalyst preparation. Hence this is an Object much referred to elsewhere. Fed-batch operation is currently included under this Object, but could also be separated as a different Object. Continuous supply of a gas (e.g. by sparging) is also included, if the process is otherwise run as a batch or fed-batch.
Many of the attributes are only usually expected under particular circumstances: GasSupply only if this is used; FedBatchDetails only if operated as a fed-batch; MixingConditions only where the medium is a MultiPhaseDispersion or a fed-batch is used; BatchTime is not expected for a biocatalytic reaction; DispersionSampling only when the medium is a MultiPhaseDispersion; SampleQuenching only when the samples may contain active biocatalyst, so the reaction must be stopped before analysis.

- Temperature
  - Type: float
  - description: The temperature held during the incubation. At present taken to be constant - coverage of programmed T to be dealt with later
- Pressure
  - Type: float
  - description: The pressure applied during the incubation - only required if not atmospheric
- TheMedium
  - Type: Solution, MultiPhaseDispersion
  - description: What is being incubated, a solution or a multi-phase dispersion
- GasSupply
  - Type: GasSupplyDetails
  - description: Details of a gas being supplied continuously to the reaction mixture, usually by sparging, if this is done
- FedBatchDetails
  - Type: pHmeasureAdjust, ConcentrationControl, ProgrammedFeed
  - description: If this is a fed-batch, information on the details. To cover three categories: 1) Control of pH; 2) Feeding in an attempt to control other concentrations based on measurements during reaction, like remaining reactant concentration; 3) Feeding according to a pre-set schedule, either continuous slow feed or time-programmed dosing.
- MixingConditions
  - Type: ShakenVessel, StirredReactor, FlowImpelledReactor
  - description: Description of features affecting mixing during the incubation (vessel, agitation etc). Normally only required when a multi-phase dispersion is incubated. The type of vessel is closely linked to the way its contents are agitated. So a vial, a well-plate or a simple flask can only be agitated by some form of shaking. Anything with a mechanical stirrer will be classified as a stirred reactor - this can range from a large vial or beaker with a magnetic flea inside to a highly engineered reactor with multiple ports and sensors. A third possibility is a reactor mixed by gas or liquid pumping.
- BatchTime
  - Type: float
  - description: The time for which the batch is incubated. Not normally needed when this is a monitored biocatalytic reaction, as times will be implicit in the results. But required where the incubation is a step in biocatalyst preparation or modification.
- DispersionSampling
  - Type: RepresentativeSample, OnePhaseSample
  - description: Details of sampling procedures when the medium is a MultiPhaseDispersion. Options are to attempt to obtain a representative sample of the whole mixture, or to separate phases and take a sample of just one. (Sampling multiple separated phases is possible but unusual, possible future extension).  
- SampleQuenching
  - Type: StopReagent, TemperatureChange
  - description: Methods used to stop the reaction before analysis, when the samples will still contain active biocatalyst. At present covers rapid mixing with a solution that acts as a stop reagent, or use of temperature (heat treatment, or cooling). Other options may be added later.


### MultiPhaseDispersion

Description of a mixture with more than one phase, which might may include liquids, solids or gases. This might be a reaction mixture, or something used in another operation, e.g. as part of an immobilisation protocol. The order in which items were added can be important, so should be recorded unless it is known to have no effect. Measurement or adjustment of pH needs to be recorded at the correct position in the sequence of making up the mixture, which might not be at the end.

- AddedItem
  - Type: AddedItemwithAmount
  - description: Details of a liquid, biocatalyst or solid item added to the mix.
  - Multiple: True
- pHmeasuredAdjusted
  - Type: pHmeasureAdjust
  - description: A measurement of pH in the solution or an adjustment to a target value - as noted, need to specify at what stage in making up the mixture the pH was measured

### AddedItemwithAmount

Description of one item added in making up a multi-phase mixture (where not everything dissolves to make a homogeneous solution). Where a gas phase is significant, needs to be handled a bit differently, goes with vessel - needs future attention in draft data model.

- AnItem
  - Type: Solution, BiocatalystUsed, DescribeSolid
  - description: Details of an item added to the mix. This could be: 1) A solution or a neat liquid; 2) a soluble enzyme or biocatalyst; 3) a solid biocatalyst; 4) a solid item other than a biocatalyst
- AmountAdded
  - Type: float
  - description: The amount of the item added. This might be given as an absolute amount or volume, e.g. g or L. It might also be given relative to one other defined item in the mixture, e.g. g per L liquid, L per g biocatalyst. The same basis for amount added needs to be used for all items added to the same mixture.


### Solution

Description of a liquid or solution. A single liquid phase, usually containing dissolved solutes. This might be a reaction mixture, an item added in preparing a multi-phase reaction mixture, or something used in another operation, e.g. as part of a modification or immobilisation protocol. Measurement or adjustment of pH (if used) needs to be recorded as at a defined stage of making up the solution, which might not be at the end when all solutes have been added. (An alternative way to record this would have two different attributes, ASoluteAddedBeforepH and ASoluteAddedAfterpH. But in general we need a system to record the sequence in which events occur here, and in many other sections.)

- MainSolvent
  - Type: SmallCompound
  - description: Details of the (predominant) solvent. (If two are in equal amounts, choose either and enter the other as a dissolved compound.)
- ASolute
  - Type: SolutewithConc
  - description: Details of one solute (a small molecule, a biocatalyst or another macromolecule) added to the solution
  - Multiple: True
- pHdetails
  - Type: pHmeasureAdjust
  - description: A measurement of pH in the solution, or an adjustment with acid or alkali to a target value - as noted, need to specify what was present at the stage this was done.


### SmallCompound

Defines a small molecule or salt used as part of a recipe. At least one of the identifiers is required to avoid ambiguity. Source and purity are only required where it is believed they may have significant effects.

- CompoundName
  - Type: string
  - description: usual name of the substance, e.g. as obtained from PubChem
- InChi
  - Type: string
  - description: InChi string
- IUPAC
  - Type: string
  - description: IUPAC name
- ChEBI\_ID
  - Type: string
  - description: identifier in ChEBI database
- PubChem\_CID
  - Type: string
  - description: identifier in PubChem database
- Source
  - Type: SuppliedProduct, SelfProducedMaterial
  - description: source of compound, either from supplier or made in-house. Not needed for common commercial chemicals unless thought significant
- Purity
  - Type: string
  - description: typically expressed in percentage (%) of the pure of desired compound relative to the total mass or volume of the compound

### SolutewithConc

Defines the identity and concentration of an item dissolved in a solution. 

- TheMaterial
  - Type: SmallCompound, BiocatalystUsed, Macromolecule
  - description: Details of the material added: a small molecule or salt solute; an enzyme or biocatalyst that will dissolve completely; or a non-enzyme macromolecular component
- Concentration
  - Type: float
  - description: concentration of the substance. This requires units e.g. M, g/L, %v. For the biocatalyst the units could be based on activity assayed in a defined reaction, so e.g. U/L.


### pHmeasureAdjust

Report a pH measurement, on a solution or multi-phase mixture, normally using a glass electrode. To include adjustment of pH to a desired value, either once during preparation, or continuously during a reaction. Some attributes will only be required in particular circumstances: NonaqueousCalibration only in the unusual case when calibration is not done with dilute aqueous standard buffers; Titrant only when this is added to adjust/control pH; ControlSystem only when continuous pH control is applied.

- pHvalue
  - Type: float
  - description: the directly measured value, which may be a targeted value after adjustment or during control (The value should not be adjusted, e.g. to different temperature, although this could be reported in text notes).
- MeasurementTemperature
  - Type: float
  - description: temperature of the solution or mixture when measured
Describes details of the calibration of a glass or other pH electrode, later used to measure, adjust or control pH. Usually calibration will be in dilute aqueous solution, but details are required if not.
- CalibrationTemperature
  - Type: float
  - description: temperature at which the electrode was calibrated
- NonaqueousCalibration
  - Type: string
  - description: report the calibration medium where this is not dilute aqueous (e.g. aqueous-organic mixture) and the nature of the set buffers used
- Titrant
  - Type: Solution
  - description: the acid or alkali used to adjust pH where this occurs
- ControlSystem
  - Type: string
  - description: When pH is continually controlled, details of the control algorithm and equipment used, perhaps with the achieved range of pH around the set point


### Macromolecule

Describes a macromolecular compound (other than an enzyme) used in an experiment. This Object still needs work to give comprehensive coverage, probably with introduction of sub-tables.

- CompoundName
  - Type: string
  - description: accepted name for the material, ideally as used in an authoritative database
- DatabaseUsed
  - Type: string
  - description: name of a database that gives further details on the material
- Identifier
  - Type: string
  - description: identifier in this database
- Source
  - Type: SuppliedProduct, SelfProducedMaterial
  - description: source of compound, either from an external supplier, or produced in house
- Purity
  - Type: string
  - description: information appropriate to the class of compound used

### SelfProducedMaterial

Details of the preparation in-house of a material (other than a biocatalyst) used in the experiments
- ProductionMethod
  - Type: string
  - description: Details of the protocol, perhaps a literature reference. Might become a more structured table later

### DescribeSolid
Details of a solid phase, other than a biocatalyst, added in making a multi-phase dispersion. Needs expansion to a fuller table, at present just a place-holder
- Details
  - Type: string
  - description: some information



### GasSupplyDetails
Details of a gas being supplied continuously to the reaction mixture, usually by sparging

- Composition
  - Type: string
  - description: The composition of the gas stream. "Air" is acceptable, otherwise give mole/volume fractions of each gas present, e.g. 100% O2, 20% CO2 plus 80% air.
- FlowRate
  - Type: float
  - description: Best given as volume per volume reaction mixture per unit time
- ContactSystem
  - Type: string
  - description: More or less a placeholder, will later require details of reactor



### ConcentrationControl
Fed-batch in which measurements during the reaction are used to decide feed schedule

- MonitoredSpecies
  - Type: MeasuredSpecies
  - description: the species whose concentration is measured in order to choose feed schedule. It is possible that more than one species may be measured to provide basis for feeding - might need extension to cover this
- FeedAlgorithm
  - Type: string
  - description: how the amount or rate of feed is calculated based on the concentration measured. Should probably be extended to a sub-table that obtains more structured information
- FeedMade
  - Type: Solution, MultiPhaseDispersion, DescribeSolid
  - description: The composition of the feed entering the batch. At present restricted to one type of feed.


### ProgrammedFeed
Details of feed to a batch programmed in advance of run. Only one of ContinuousFeedRate or DosingSchedule is expected.

- FeedMade
  - Type: Solution, MultiPhaseDispersion, DescribeSolid
  - description: The composition of the feed entering the batch. At present restricted to one type of feed.
- ContinuousFeedRate
  - Type: float
  - description: The flow rate of the feed, normally as a ratio to the volume of the reaction mixture, e.g. 0.001 volume per volume per hour
- DosingSchedule
  - Type: string
  - description: Details of when and how much feed is added, e.g. 0.01 volume per volume every 3 hours. Should become a more structured table. 


### ShakenVessel
Reports details of a vial, a well-plate or a simple flask agitated by some form of shaking. Multi-well plates are commonly used in high throughput experiments. Effectively these consist of an array of identical vials in which individual reactions occur. So the conditions for any one reaction are based on the properties of a single well.

- ShapeDimensions
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- Materials
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- Sealing
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- ShakingDetails
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.


### StirredReactor
Reports details of a vessel with a mechanical stirrer - this can range from a large vial or beaker with a magnetic flea inside to a highly engineered reactor with multiple ports and sensors. 

- ShapeDimensions
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- Materials
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- ImpellerTypeDimensions
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.
- ImpellerRate
  - Type: string
  - description: More a placeholder at present, need to bring in more detailed attributes, mostly to be copied from existing model.

### FlowImpelledReactor
A reactor mixed by a flow of gas or liquid pumped externally.

- Properties
  - Type: string
  - description: Just a placeholder, needs extension to fuller table and probably sub-tables


### RepresentativeSample
Details of how a (hopefully) representative sample is obtained from a MultiPhaseDispersion.

- Procedure
  - Type: string
  - description: How this is achieved, usually by sampling while under vigorous mixing, and perhaps evidence that the sample really is representative. Mainly a placeholder, to be extended to more structured table.


### OnePhaseSample
Details of how a sample of just one phase in a MultiPhaseDispersion is obtained

- PhaseSeparation
  - Type: Separation
  - description: Details of how the phases are separated, using type defined in Biocatalyst file (although needs more detail in its definition there). 
- PhaseSampled
  - Type: string
  - description: Which of the separated phases is sampled. Might become an enumerated list, e.g. supernatant liquid, organic phase, wet precipitate.


### StopReagent
Details of how the reaction is stopped by rapid mixing with a stop reagent

- TheReagent
  - Type: Solution
  - description: The composition of the stop reagent used
- StoptoSampleRatio
  - Type: float
  - description: The volume of stop reagent added, as a ratio to the sample volume. i.e. parts of stop reagent per 1 part sample, may be fractional

### TemperatureChange
Details of a treatment to stop reaction in samples before analysis

- TemperatureApplied
  - Type: float
  - description: the higher temperature applied to inactivate the biocatalyst, or the lower temperature to greatly reduce its activity
- HoldTime
  - Type: float
  - description: how long the heat treatment is applied for, or the maximum time held after cooling before analysis


### ContinuousReaction
Details of the operation and results of a continuous reactor. This needs a lot of extension, including for reporting of results - usually composition of liquid stream exiting reactor. At present just shows how will re-use Solution Object.

- FeedComposition
  - Type: Solution
  - description: Details of the liquid fed continuously to the reactor
- FeedRate
  - Type: float
  - description: Flow rate of feed to the reactor. Could have units based on absolute volume, or as ratio to volume of reactor (i.e. “dilution rate”).
- ReactorType
  - Type: string
  - description: The type of reactor: CSTR and PackedBed are the two obvious possibilities, each should become a separate Object referred to here. But will later need expanded to include more possibilities, membrane reactors etc




Temp placeholder for end of document - leave or can cause problems!!!!!



