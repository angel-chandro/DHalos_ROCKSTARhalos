# DHalos_ROCKSTARhalos
Gather information about running DHalos with ROCKSTAR halos.


This repository contains files related to running ROCKSTAR halos on DHalos (or at least try it) that have been created or modified. The modifications and additions are briefly explained, but they can be observed more accurately in my DHalos repository here [Link](https://github.com/angel-chandro/DHalos) (they can have comments attached). The different files are:

First of all, to run DHalos we need to provide a redshift list file that contains 3 columns: snapshots (0), scalefactors(1) and redshifts(2) of each of the different timesteps of the UNIT simulation. Here I have created a python file to generate this redshift list input file. Moreover, I have modify DHalos to work with the scale factor column that it wasn't used before.

* write_redshift_list.py: python file used to create redshift list file with 3 columns as explained above. The format is: snapshot(0), scalefactor(1), redshift(2).

* DHalos/Build_Trees/tree_files.f90, DHalos/Build_Trees/subhalo_data.f90: "sfactor" variable that refers to scale factor included (because redshift_list file has the format: snapshot(0), scalefactor(1),  redshift (2) in 3 different columns).

So far the current strategy is to generate the halo trees based on ROCKSTAR halo catalogues and to do that, it is necessary to know if the halo is a host halo ("pid" = -1) or is a subhalo that belongs to a host halo ("pid" = host halo ID). Therefore, it has been run the find_parents tool over different ROCKSTAR halo list outputs (in particular the /data5/UNITSIM/1Gpc_2048/FixAmp_001 simulation in taurus) in order to have only a 2 level halo hierarchy. This find_parents tool adds a column with "pid": parent ID property to the halo catalogues.
The other strategy that has been left aside for the moment is to run DHalos over ConsistentTrees halo catalogues, what is a possibility since those catalogues have a "pid" (ID of least massive host halo (-1 if distinct halo)) column (different from the one above) and other "upid" (ID of most massive host halo (different from Pid when the halo is within two or more larger halos) column (here we have a 3 level hierarchy). It has been checked that both columns only differing in a 1% of the halos, so "upid" column could be used to distinguish host halos and subhalos in this case.

* ROCKSTAR_parent_test.py: python file used to run find_parents tool over different ROCKSTAR halo list outputs (this find_parents tool adds a column with "pid": parent ID).

Until now it has been created or modified the DHalos files in order to read ROCKSTAR halo catalogues. As ROCKSTAR catalagues already have descendants information, it is not necessary to create a descendants file. But it is essential to match up the DHalos halo properties with ROCKSTAR halo properties to run DHalos perfectly.
Moreover, it seems the halo ID format necessary to generate DHalos halo trees requires an "IDCorr" variable in halo IDs and descendant halo IDs because nifty_reader.f90 included it and nifty ID format is almost the same as ROCKSTAR). So in principle, IDCorr must keep as in "nifty_reader.f90" file and add itto descendants ID in this case (maybe information here [Link](http://gavo.mpa-garching.mpg.de/Millennium/pages/help/HelpSingleHTML.jsp#identifiers)).
**Update**: I have run DHalos using "IDCorr" variables in both halo IDs and descendant IDs and it seems to work, because descendant connections are found correctly.
**Current problems**: when running DHalos 0 subhaloes are interpolated and 0 subhaloes are isolated, which I don't know if it means that something is still failing or it is due to ROCKSTAR haloes format and everything is already okay. What has work to do for sure is to finish finding the correspondences between DHalos halo catalogue properties (marked as input in subhalo_data.f90 file) and the ones included in ROCKSTAR halo catalogues: specially mostboundID, cofm, mgas, cnfw, Vvir and mass; apart from checking the units and definitions of the ones already matched up. 

* DHalos/ROCKSTAR.test.param: parameter file for ROCKSTAR haloes that has been created.
Modifications: subfind_format = ROCKSTAR, ROCKSTAR_path included (where you can find ROCKSTAR halo catalogues), ROCKSTAR cosmological parameters and particle mass, desc_file = none (not necessary with ROCKSTAR) and hydrorun = .false

* DHalos/Build_Trees/ROCKSTAR_reader.f90: file created to read ROCKSTAR haloes so that DHalos makes the halo trees. Copied from nifty_reader.f90, but functions names have been changed to read_ROCKSTAR and ROCKSTAR_find_main_progenitors. But only read_ROCKSTAR function has been internally varied: "nsubd", "haloid", "descid", "dsnap" variables have been removed because it doesn't need descendants file; modified halo properties just as they appeared in ROCKSTAR halo catalogues; when reading halo catalogue it is necessary to skip header but in this case header has more than 1 line (not as nifty catalogue) so modifications have been made to solve this including "nhead" counter in order to count header lines; also as ROCKSTAR halo ID can be equal to 0 some conditions have been changed.

* DHalos/Build_Trees/merger_trees.f90: ROCKSTAR_reader and subfind_format = ROCKSTAR included.

* DHalos/Build_Trees/CMakeLists.txt: ROCKSTAR_reader included.

Useful information about the project can be found in different links below:
* DHalos info: [Link](https://arxiv.org/pdf/1311.6649.pdf)
* ROCKSTAR info: [Link](https://arxiv.org/pdf/1110.4370.pdf), [Link](https://arxiv.org/pdf/1110.4372.pdf)
* UNIT info: [Link](https://arxiv.org/pdf/1811.02111.pdf)
* SHARK info: [Link](https://arxiv.org/pdf/1807.11180.pdf)
* SAMs info: [Link](https://arxiv.org/pdf/1412.2712.pdf), [Link](https://arxiv.org/pdf/astro-ph/0610031.pdf)
* ROCKSTAR concentration info: [Link](https://arxiv.org/pdf/2007.09012.pdf) (4.3 section)
* Calibration model info: [Link](https://arxiv.org/pdf/2103.01072.pdf)
* Approximations made on baryonic matter info: [Link](https://arxiv.org/pdf/1804.03097.pdf)
* Differences ROCKSTAR-VELOCIraptor in DHalos info: [Link](https://arxiv.org/pdf/2106.12664.pdf)
