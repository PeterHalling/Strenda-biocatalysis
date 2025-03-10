
# Peter Halling network data model for applied biocatalysis - file focussing on biocatalyst attributes

This file has cross-references to Objects in other files in order to make up the whole model. The whole model is a draft and does not yet include all the attributes we have identified, so it would need extended. The aim is to show how attributes can be grouped to minimise repetition - so that each attribute appears only once whenever possible. Thus details about description, requirements, validation etc can be specified (and subsequently coded) just in one place for each group of attributes. This draft is obviously based on the data model we have worked on extensively and published, and also takes inspiration from ideas from others, particularly Carsten Kettner and Jan Range.


### ProteinDescription

Describe the chemical nature of a protein used as an enzyme, or present as one active component of a more complicated biocatalyst. In some cases (e.g. a commercial product) some of this information may be unknown - if so, it is better to give no information rather than enter guesses.

- ProteinName
  - Type: string
  - description: name of the protein, if possible as used by UniProtKB or other database. The name will usually have a generic part based on the catalyzed reaction, for example 'lipase', and also designate a specific type by naming a source organism and/or code, such as '_Bacillus amyloliquefaciens_ alpha-amylase A'.
- ProteinSequence
  - Type: string
  - description: amino acid sequence using one letter code
- UniProtKBlink
  - Type: string
  - description: identifier code if sequence in UniProtKB, or explain relationship to a UniProtKB sequence (e.g. an engineered mutant)
- PostTranslationalModification
  - Type: DescribePTM
  - description: To be used to describe modifications made in the cell during expression. Chemical modifications made during biocatalyst preparation should be described as part of FormulationModification.
- EnzymeClassification
  - Type: string
  - description: EC number, and perhaps further information about reference reaction catalysed

### BiocatalystUsed

Describes metadata about a biocatalyst as introduced into the reaction mixture to catalyse a reaction. It might consist of a single BiocatalystPreparation, which has been obtained from an external source or produced in-house. But it might also be the result of processes to formulate, modify or immobilise one or more BiocatalystPreparation items. The BiocatalystUsed may dissolve completely in the reaction mixture, or it may be in form of solid particles that remain suspended. Options for the nature of the particles include: a) whole cells or fragments of them that contain active enzyme(s); b) the result of immobilisation; c) (part-)dried particles added to a non-aqueous medium.

- Preparations
  - Type: BiocatalystPreparation
  - description: Details of one or more that are, or have been used in making, the biocatalyst used in a reaction
  - Multiple: true
- FormulationModification
  - Type: FormulationModification
  - description: Collects details of any formulation, modification, immobilisation etc that takes place after the biocatalyst is obtained or produced
- StorageandStability
  - Type: StorageStability
  - description: Collects information about how the biocatalyst is stored before use, and information about its stability during storage
- IsSoluble
  - Type: boolean
  - description: Will the biocatalyst dissolve in the reaction mixture
- SolidProperties
  - Type: string
  - description: If not soluble, information about the solid properties of the material used, e.g. particle size, wetting liquid content etc. Probably should later become a reference to a more structured sub-table in a separate Object.
- StandardAssay
  - Type: StandardAssay
  - description: Reports an assay of the catalytic activity of the biocatalyst in a standard reaction, used as part of its characterisation

### BiocatalystPreparation

Describes a biocatalyst preparation that has been obtained from an external source or produced in-house. If the ProteinDescription is fully completed, and Purity is good, then source details may strictly not be needed, although they may usefully be given.

- Protein
  - Type: ProteinDescription
  - description: Collects information about the active protein(s) in the preparation used
  - Multiple: true
- Source
  - Type: SuppliedProduct, SelfProduced
  - description: Links to one of the two possible source types, a commercial or similar external source, or details of how the biocatalyst has been produced in-house
- Purity
  - Type: ProteinPurity
  - description: Collects information about purity
- AmountBasis
  - Type: string
  - description: Information about how the amount of the biocatalyst is specified, e.g. mol of pure enzyme, mg of total protein, mg of dried preparation, units (specified) of enzyme activity in defined assay
- StorageandStability
  - Type: StorageStability
  - description: Collects information about how the biocatalyst is stored before use, and information about its stability during storage
- StandardAssay
  - Type: StandardAssay
  - description: Reports an assay of the catalytic activity of the biocatalyst in a standard reaction, used as part of its characterisation


### StandardAssay

Reports an assay of the catalytic activity of the biocatalyst in a standard reaction, used as part of its characterisation. Note this is not the target biocatalytic reaction, which should be reported via the separate Object for this. Rather this refers to measurements of activity in some reference reaction, often with model substrates, used to assess the properties of the biocatalyst.

- AssayConditions
  - Type: BatchIncubation
  - description: Reports the conditions for the standard assay
- MeasuredActivity
  - Type: string
  - description: More or less a placeholder at present, should become more detailed, perhaps a sub-table

### SuppliedProduct

Collect details where a biocatalyst or other material used is characterised by its provenance, usually from a commercial supplier

- Supplier
  - Type: string
  - description: Information about the supplier from which the material was obtained
- Name
  - Type: string
  - description: Name as given by the supplier
- ProductCode
  - Type: string
  - description: Reference code which identifies the product unambiguously among the range available from that supplier
- LotNo
  - Type: string
  - description: A lot or batch number for the material used

### SelfProduced

This section describes how a biocatalyst has been produced in the user's own lab. This will typically be by expression in cultured cells, but could also be via cell-free expression systems, or by extraction from bulk biomass.
I have retained attributes below that have been listed earlier by others, but I don't want to say too much about this topic because I don't have the necessary expertise. We should also note that most of the relevant methods here are common to other application areas in which proteins are expressed, and we might hope to build a consensus with experts from all these areas. We certainly need more details about the culture media, growth times, cell free expression medium etc. Also probably recovery, any purification. Some of these are probably best handled in sub-tables.

- ExpressionSystem
  - Type: string
  - description: description of expression system if heterologously expressed, including the organism which was used as heterologous expression system
- SequenceDNA
  - Type: string
  - description: The DNA sequence of the biocatalyst including any tags and linkers.
- SequencePlasmid
  - Type: string
  - description: The DNA sequence of the plasmid used to produce the biocatalyst. The sequence can be provided in plain text or as a database ID.
- SequenceSpecification
  - Type: string
  - description: All DNA sequence changes (e.g. codon optimization for E. coli, insertion of affinity tags, sequence truncation, etc.) should be provided.
- OriginOrganism
  - Type: string
  - description: The specific species or source from which the enzyme is derived or isolated, ideally with TaxonID
- Purification
  - Type: string
  - Description: The choice of purification methods is diverse and can impact the enzyme, with possible methods including chromatographic techniques, precipitation, HPLC, ultrafiltration, dialysis, salt fractionation,      etc.


### ProteinPurity

- Purity
  - Type: float
  - description: Purity of enzymes typically expressed in percentage (%). It is usually stated as the percentage of the pure enzyme or active component relative to the total mass of the enzyme preparation.
- PurityDetermination
  - Type: string
  - description: Description of how the purity of the biocatalyst was determined. For purchased enzymes this might be a value quoted by the suppliers, but this might also be independently measured by the user.
- SpecialTreatment
  - Type: string
  - description: description of any further procedures or aspects relevant to the characterization of the protein and reproducibility of the generation of the protein

### FormulationModification

This section deals with any treatment applied after the biocatalyst is purchased or produced, but before it is introduced to the reaction mixture. This includes chemical modification and immobilisation. There is often no completely unambiguous dividing line between the process of production and that of further modification.  In general, expression, extraction, purification and isolation would be considered part of production. But placing the dividing line in different places should not prevent the necessary metadata details being captured in one of the tables.

The process of formulation or modification can generally be described as a sequence of steps that can be viewed as either incubations, separations or characterisations. Hence the metadata required is the description of these steps in the correct sequence. Each of them could appear more than once, normally with different details. The results of characterisation might be classed as data rather than metadata, so I'm not quite sure how best to record them. It is also conceivable that continuous process steps might be used, might need future provision for this.

- AnIncubation
  - Type: BatchIncubation
  - description: Details of one batch incubation step
  - Multiple: True
- ASeparation
  - Type: Separation
  - description: Details of one separation step
  - Multiple: True
- ACharacterisation
  - Type: Characterisation
  - description: Details of one characterisation step. At present just a placeholder if these end up being recorded here.
  - Multiple: True

### Separation

Describes an operation in which two or more parts of a mixture are separated from each other, usually following an incubation. Typically a solid material will be separated from a supernatant liquid. Separation operations are often part of a protocol for chemical modification or immobilisation. Often just one of the separated materials will be desired for further processing or use, and the other will be discarded, perhaps after analysis. Separation may also include a washing step, and the washings may be combined with the initial supernatant, or may be discarded. Solid-liquid separations will usually be via settling under gravity, centrifugation or filtration. In future, might also need to allow for liquid-liquid separation. At present this Object is just a placeholder.
- SeparationDetails
  - Type: string
  - description: The details. Should expand to structured table


### Characterisation

Describes a characterisation of a biocatalyst, either the final product or an intermediate stage in production or modification. At present just a placeholder, as not sure whether this should be classed as metadata or handled separately as data.
- AMeasurement
  - Type: string
  - description: Details of the measurement. Should later become a more structured table.


### DescribePTM

Collect details of post-translational modifications that take place during expression of the protein. Probably better to turn into a more structured table later.

- PTMDetails
  - Type: string
  - description: Details of Phosphorylation, Glycosylation, Acetylation, Hydroxylation, Methylation, Other. Should be extended into more detailed table with specific fields


### StorageStability

Details of how the biocatalyst was stored after preparation and before use (if it was not used immediately). Only one of WetUnfrozen, Frozen or Dried is expected.

- WetUnfrozen
  - Type: BatchIncubation
  - description: collect full details of medium, temperature etc
- Frozen
  - Type: FrozenStorage
  - description: details of frozen storage method
- Dried
  - Type: DryStorage
  - description: details of storage by drying
- MaximalStorageTime
  - Type: float
  - description: longest time of storage under the reported conditions before use
- StatementLoss
  - Type: string
  - description: Statement about observed loss of activity under the above conditions

### Freezing

Describes a process of freezing a sample. Might be a precursor to frozen storage, or freeze drying. 
- WhatisFrozen
  - Type: Solution, MultiPhaseDispersion
  - description: The solution or multi-phase mixture that is to be frozen
- FreezeTemperature
  - Type: float
  - description: The target temperature for the freezing process
- FreezingProcess
  - Type: string
  - description: Any relevant details of the freezing process, e.g. equipment, cooling rate

### FrozenStorage

Describes storage by freezing and holding at low temperature

- InitialFreezing
  - Type: Freezing
  - description: The process of initial freezing
- HoldTemperature
  - Type: float
  - description: The temperature maintained through the storage process, e.g. -20 or -80 oC
- ThawProcess
  - Type: string
  - description: Any relevant details of how the sample is thawed before use

### DryStorage

Describes storage by drying. Only one of FreezeDrying, AirDrying or SprayDrying is expected - so once these are codes as separate Objects, would have attribute DryingMethod with 3 possible types

- FreezeDrying
  - Type: string
  - description: Details of a freeze-drying process. Should become a structured table, using Freezing as a sub-table
- AirDrying
  - Type: string
  - description: Details of an air-drying process. Should also become a structured table.
- SprayDrying
  - Type: string
  - description: Details of a spray-drying process. Should also become a structured table.
- HoldTemperature
  - Type: float
  - description: Temperature at which the dry preparation was held
- Rehydration
  - Type: string
  - description: Any significant details of how the biocatalyst is re-hydrated before use




Temp placeholder for end of document - leave or can cause problems!!!!!



