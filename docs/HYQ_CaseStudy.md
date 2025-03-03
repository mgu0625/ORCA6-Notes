# Hydroquinone (HYQ) Computational Case Study

### 1 Hartree (Eh) = 27.2114 eV = 630 kcal/mol = 2600 kJ / mol = $2.2 x 10^5 cm^{-1}$ = 1/13.6 Bohr

### 1. Introduction & Experimental Relevance
HYQ is an aromatic organic compound with formula $C_6G_4(OH)_2$ consisting of 14 atoms. 

It is widely studied due to its redox properties, bonding capabilities, and role in various chemical and biological processes;

Commonly used for:
	- **Photochemistry**: Excellent reducing agent, critical for radical chemistry
	- **Electrochemistry**: Undergoes reversible oxidation to quinone
	- **Spectroscopy**: Characterized via UV-vis, IR and Raman Spectroscopy
	- **Solubility & Solvent Effects**: Solubility varies in different solvents, making **solvent modeling** crucial for comparison with experimental results

References for HYQ Studies:
- 10.21577/0103-5053.20170022
- 10.1039/D2NJ04878G
- 10.1021/jp200381n

-----

### Goals for Case Study
****Systematically analyze HYQ using DFT calculations in ORCA 6.****

1. Gas-Phase Structural and Vibrational Analysis

- Perform geometry optimization and frequency calculations using seven different DFT functionals.
- Compare results across Generalized Gradient Approximation (GGA), Hybrid-GGA, and Meta-GGA functionals.
- Assess influence of auxiliary basis sets on computational accuracy and efficiency

2. Solvent Effect Analysis

- Evaluate solvation effects using CPCM solvation model and CPCM with surface-type Gaussian
- Compare structural and electronic properties in acetonitrile, DMSO, n-hexane, and water
- Conduct calculations using PBE, PBE0, and B3LYP functionals to examine solvent and functional interplay

3. MO and NBO analysis
- Extract MO data using different ORCA's built-in MO printing commands
- Perform single-point energy calculation for NBO analysis 
- Investigate electronic structure changes in different solvent environments

------

# Step-by-Step Computational Workflow

## 1. Gas-Phase Optimization and Frequency Calculation

Functionals Used
- GGA: PBE, BLYP, BP86
- Hybrid-GGA: PBE0, B3LYP
- meta-GGA: TPSS, B97M-D3BJ

Basis Set Used: def2-SVP

**Sample Input files in /ORCA6-NOTES/input_files**

Things to Note:
   - **TightSCF**: Used to ensure more accurate SCF convergence by reducing the energy and density error thresholds, resulting in a more precise electronic structure calculation.
   - **VerytightOPT**: Used to impose stricter convergence criteria on optimization calculation, ensuring minimal residual forces on atoms and resulting in a highly optimized structure with accurate bond lengths and angles/
  
#### After Calculation of Geometry Optimization
- Check bottom of `.out` file to ensure calculation terminated
  
	<img width="509" alt="image" src="https://github.com/user-attachments/assets/4692d3a0-1eec-430a-951a-51a238e418d9" />

- Use ChemCraft (Reccommended for ORCA users) or other visualizing software (Avogadro, VMD, MacmolPlt etc.,) to visualize optimized structures

- Takeaways from HYQ calculation:
  	- ΔE (in eV) was calculated as: $ΔΕ(eV) = (Ε_{functional} - E_{lowest}) x 27.2114 = (E_{first reported in .out} - E_{final single point energy})$
  	- $E_{final}$ located towards botton of .inp
  	  
  	  <img width="615" alt="image" src="https://github.com/user-attachments/assets/1a8fbf7c-d9bc-4403-8c75-be5d0217fab0" />
 
        - $E_{initial} is found right under where it **first** sais SCF Converged
  	 
  	       <img width="716" alt="Screenshot 2025-03-03 at 9 05 24 PM" src="https://github.com/user-attachments/assets/c297f681-8743-4bac-a403-46be79979dc4" />


       	- Python Script (attached to /script) was used to extract bond lengths and RMSD
  	    	- automated by parsing .xyz file to find the optimized coordinate and to reference for evaluating structural deviation (Make sure to know Atom Numbers, can visualize using Avogadro)


**Sample `.out` files in /ORCA6-NOTES/output_files**

##### From Geometry Optimization of HYQ at Gas-Phase (Comparison of Functionals
| Functional Name | Aux Basis | Final SCF Energy (Hartree) | ΔE (eV) | Computational Time |
| :---: | :---: | :---: | :---: | :---: |
| BLYP | none | -382.2830706 | -0.313556003 | 2 min 8.769 sec | 
| --- | def2/J + RI on | -382.2834179 | -0.313309135 | 55.29 sec |
| PBE | none | -381.9640679 | -0.304378283 | 2 min 20.10 sec |
| --- | def2/J + RI on | -381.9644084 | -0.304163873 | 56.142 sec |
| B3LYP | none | -381.9829343 | -0.267288528 | 2 min 15.324 sec |
| --- | def2/J + RIJCOSX | -381.9832893 | -0.267177549 | 4 min 2.50 sec |
| --- | def2/J + LibXC | -382.1967823 | -0.255921052 | 3 min 58.418 sec |
| PBE0 | none | -381.9829343 | -0.267288663 | 2 min 13.679 sec |
| --- | def2/J + RIJCOSX | -381.9832893 | -0.267177594 | 3 min 57.690 sec |
| --- | def2/J + RIJK | -381.9828504 | -0.267396184 | 1 min 39.645 sec | 
| BP86 | none | -382.420062 | -0.305415524 | 2 min 21.268 sec |
| --- | def2/J + RIJCOSX | -382.420062 | -0.305415524 | 57.28 sec |
| B97M-D3BJ | def2/J + RI on | -382.6225074 | -1.16304756 | 1 min 9 .722 sec |

- Best Functional:
  	- Best ΔE which is the lowest reported B3LYP + LibXC being -0.256 eV
  	- Second best was PBE0 with RIJK (better approximation compared to RIJCOSX) withΔE being -0.2674 eV for RIJK used and -0.2672 eV for RIJCOSX used

- Worst Functional:
  	- meta-GGAs, gives large |ΔE|, showcasing significant shift.

- Computational Cost vs Accuracy:
  	- Some basis sets converged faster but most likely sacrificed accuracy.
  	- RIJCOSX-based hybrid-GGA functionals (e.g., PBE0, B3LYP) showed more accuracy HOWEVER, required longer computational times.
  	- def2/J + RI on sets optimized under a min while using RIJCOSX increased computational time to under 5 minutes.
  	- RIJK computed faster compared to RIJCOSX (PBE0) even though RIJK is more computationally expensive

#### After HYQ Frequency Calculations 
- vibrational frequencies (towards bottom of `.out`) was checked to make sure there's no imaginary frequencies

<img width="479" alt="image" src="https://github.com/user-attachments/assets/386be11e-0a6f-4b74-96c7-7990f1027027" />

- Zero Point Energy (ZPE) correction found towards bottom of `.out` file

  <img width="655" alt="image" src="https://github.com/user-attachments/assets/a1d3c88e-e20a-4383-80df-afab1f241e28" />

- Recorded $ΔΗ_{corr}, ΔG_{corr} and ΔS values

| Functional Name | Aux Basis | ΔE(eV) | Final SCF Energy (Eh) | ZPE (kcal/mol) | $ΔΗ_{corr}$ (Eh) | $ΔG_{corr}$ (Eh) | $ΔS_{final}$ (kcal/mol) | Computational Time |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| PBE0 | def2/J + RIJCOSX | -4.56349E-08 | -381.9832893 | 68.98 | -381.8657428 | -381.9038059 | 23.88 | 3 min 56.668 sec |
| B3LYP | RIJCOSX + LIBXC | -6.33209E-08 | -382.1967823	| 68.25 | -382.0803708 | -382.1184962 | 23.92 | 3 min 51.463 sec | 
| PBE | def2/J + D3BJ | -0.278910525 | -381.9746582 | 2.51 | -381.8607585 | -381.8990914 | 24.05 | 1 min 25.441 sec |


#### Notes from Concluding Frequency Calculation on HYQ
- B3LYP showed most stable energy with lowest final SCF energy (-382.197 Eh) suggesting better stabilization in comparison to PBE0.
- PBE showed least stable energy structure with highest final SCF energy (-381.861 Eh) suggesting less stable electronic structure
- PBE showed highest ZPE energy, indicating inadequate vibrational scaling or missed contributions (most likely from Error of attempting to use D3BJ with parameters).
- Corrected $ΔΗ_{corr}$ and $ΔG_{corr}$ followed trend of B3LYP > PBE0 > PBE, reinforcing B3LYP's stronger stabilization
- Similar $ΔS_{final}$ indicate similar vibrational contributions across all functionals.
- B3LYP overall showed most thermodynamically stable functional for this system with PBE0 being comparatively stable but with slightly less stability as indicated by higher energies. 
 
Error when attempting to use D3BJ Dispersion Correction with PBE functional (Following DOI: 10.1063/1.4907719 and DOI: 10.1021/acs.jpclett.6b00780)

- Most likely due to ORCA's limited support for D3BJ with PBE despite specifying parameters in input file.
- PBE is pure DFT and ORCA typically applices D3 for pure GGA functionals [Read About it More](https://www.faccts.de/docs/orca/6.0/manual/contents/detailed/model.html)

From `.out` file 

<img width="748" alt="image" src="https://github.com/user-attachments/assets/0560e288-487b-4fe9-9e8f-9c54cd5e3071" />


Plotting IR Spectrum (using EXCEL)

- From 0 to 2000 $cm^{-1}$

     <img width="672" alt="image" src="https://github.com/user-attachments/assets/b3816504-659f-49e0-bf13-886255b8f2c1" />

- From 0 to 500 $cm^{-1}$
  
    <img width="334" alt="image" src="https://github.com/user-attachments/assets/86c7ae69-1271-4d1b-8ec7-aa878963fa48" />

  


## 2. Solvent Effect Analysis

### CPCM Solvation Model Calculation
- Make sure to put CPCM(solventname) next to keyword and specify dielectric constant under `%cpcm`
- `surface_type Gaussian` was implemented to CPCM to determine how solvent cavity is constructed
  	- Without, default tessellataion-based cavity is constructed (calculated with and without  Gaussian-based cavity constructions)
  

##### Solent Used for HYQ Calculations:
- Acetonitrile
- Water
- DMSO
- n-Hexane

**Example input file in /ORCA6-NOTES/input_files**

### After Geometry Optimization Calculation (Same Data was Extracted as Step 1)

From Geometry Optimization


    
