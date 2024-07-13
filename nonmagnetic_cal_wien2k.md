####  Calculation in Wien2k (Non-magnetic) #######

! $ symbol means run the command in terminal

! Note just remove flag -p for serial mode

! replace the name test by according to directory name

$ makdir test

$ cd test

$ makestruct_lapw

$ cp init.struct test.struct

$ init_lapw -b -numk 5000 -vxc 13 -rkmax 6.0 -ecut -7.0

! or for new version of wien2k

$ init_lapw -b -hdlo -vxc 13 -ecut -7.0 -numk 1600 -rkmax 6.5 -lvns 6 -gmax 16.0 -fftfac 2.0

$ run_lapw -p -ec 0.00001 -cc 0.0001 -NI

$ save_lapw -d pbe

$ clean_lapw


#### band structure calculation #### 

! get test.klist_band using xcryden

$ x lapw1 -band -p 

$ x lapw2 -band -qtl -p

$ grepline :FER *.scf 1 

$ x spaghetti -p  # it will create soc.insp file

! insert fermienergy 

$ vi test.insp

$ x spaghetti -p

! plot the band structure with plotting tool

$ xmgrace test.bands.agr

$ save_lapw -band -d pbeband

#### TDOS and PDOS calculation #### 

$ vi test.in1 ! set larger value of Emax 3.0 (opional sometime)

$ x kgen -p  ! Set denser k-mesh 

$ x lapw1 -p ! steps 1-3 are not required for direct dos calculation after scf run and start from 4   but remember if you are calculating after band calculation do steps 1-3.

$ x lapw2 -qtl -p

$ configure_int_lapw ! select different orbitals

$ x tetra -p

$ dosplot_lapw

$ save_lapw -dos -d pbedos

! Note that it is better to calculate DOS before band structure. If DOS is done first then we dont need steps 1 and 2 but if done after band calculation then steps 1 and 2 are crucial.


#### Optic calculation for semiconductor ####

$ vi test.in1 ! Set larger Emax around 5 to 7 but not compulsory. Use default value if you don't have idea.

$ x kgen -p ! set denser k-mesh around 20000

$ x lapw 1 -p 

$ x lapw2 -fermi -p

$ x optic -p ! it will generate test.inop

$ vi test.inop ! edit it with proper number of k-points, axis symmetry, which looks like

###### test.inop ######

800 1 number of k-points, first k-point  ! check k-points convergence

-5.0 5.0 energy window for matrix elements (Emin,Emax)

3 number of cases ! according to choices here 3 for orthorhombic

1 Re xx

2 Re yy

3 Re zz

OFF ON/OFF writes MME to unit 4

################ end of file ###################

$ x optic -p

$ x joint -p ! it will generate test.injoint 

$ vi test.injoint ! it looks like

###### test.injoint ######################

1 18 set lower and uper band index

0.000, 0.001,1.00 Emin, dE, Emax[Ry] 

4 switch  ! proper switch for semiconductor is 4 and for metal fist switch to 6, calculate plasma frequency and then switch back to 4.

##########################################################################

$ x joint -p

$ x kram 

$ vi test.inkram ! set proper field. 

###### test.inkram #####################

## For semiconductor ## 

! 0.05 boardening gamma

! 1.000 energy shift

! 0 no intraband contribution

################################

## For metal ##

! 0.1 boardening gamma

! 0.000 energy shift

! 1 Add intraband contribution

12.6 set plasma frquency calculated before

0.2 broadning for interband

################################

$ x kram

$ opticplot_lapw 

$ save_lapw -optic -d pbe

$ clean_lapw

#### Thermoelectric calculation ####

! Perform regualr scf calculation
 
$ x kgen ! use denser k-mesh like 25000-75000

$ x lapw1 ! For case.in1c use x lapw1c 

! interpolate the results 

$ btp2 -vv -n 6 interpolate -e -0.2 -E 2.0 -m 4 ./ 

! integrate the results 

$ btp2 -vv -n 6 integrate interpolation.bt2 T1:T2:DT

$ btp2 plot -c '["xx"]' -u -s 4 pbe.btj PF ! To see plot options are S, kappae, sigma, cv,n, DOS RH, L, PF and -u, -T

$ vi case.infermi ! insert reduced Fermi energy in Rydberg here and save it

$ calculate_zt_thp 

$ boltz_plotpl2 

$ save_boltz2 -a pbete

######  This file is prepared by Pawan Joshi,M.Sc. Physics   #############

######  Central Department of Physics, Tribhuwan University (2077-2081)   ################

######  copywrite to Pawan Joshi, Email: pjoshi2357@gmail.com   #############################

######   Github: https://github.com/pawan054                     ################################
