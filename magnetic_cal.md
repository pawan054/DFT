####  Calculation in Wien2k (Magnetic-Ferromagnetic)########

! Note just remove flag -p for serial mode

! replace the name 'test' according to directory name

! $ symbol means run the command interminal

! Here I am doing for NiO

$ makdir NiO

$ cd NiO

$ makestruct_lapw !(L,F,A,4.186,2, Ni=0,0,0 and O=0.5,0,0)

$ cp init.struct NiO.struct

$ init_lapw -b -numk 5000 -vxc 13 -rkmax 6.0 -ecut -7.0 -sp

$ runsp_lapw -p -ec 0.00001 -cc 0.0001 -NI

$ save_lapw -d pbe_fm

$ clean_lapw

#### band structure calculation ###### 

! get NiO.klist_band using xcryden

$ x lapw1 -band -up -p 

$ x lapw1 -band -dn -p 

$ x lapw2 -band -qtl -up -p

$ x lapw2 -band -qtl -dn -p

$ grepline :FER *.scf 1 

$ x spaghetti -up -p  # it will create soc.insp file

$ vi NiO.insp ! insert fermienergy into NiO.insp

$ x spaghetti -up -p

$ x spaghetti -dn -p

! plot the band structure with plotting tool

$ xmgrace NiO.bandsup.agr NiO.bandsdn.agr

$ save_lapw -band -d pbe

#### TDOS and PDOS calculation ###### 

$ vi NiO.in1

$ x kgen   ! Set denser k-mesh optional

$ x lapw1 -up -p ! optional but do once if you are doing after band calculation

$ x lapw1 -dn -p ! optional but do once if you are doing after band calculation

$ x lapw2 -up -qtl -p

$ x lapw2 -dp -qtl -p

$ configure_init_lapw ! select different orbitals

$ x tetra -up -p

$ x tetra -dn -p

$ dosplot2 -up ! chose for dn too

$ save_lapw -dos -d pbe

! Note that it is better to calculate DOS before band structure. If DOS is done first then we dont need steps 1 and 2 but if done after band calculation then steps 1 and 2 are crucial.

##### Optic calculation for semiconductor ########

$ vi test.in1 ! Set larger Emax around 5 to 7 

$ x kgen -p ! set denser k-mesh

$ x lapw 1 -up -p 

$ x lapw 1 -dn -p 

$ x lapw2 -fermi -up -p

$ x lapw2 -fermi -dn -p

$ x optic -up -p ! it will generate test.inop

$ vi test.inop ! edit it with proper axis symmetry

$ x optic -up -p

$ x optic -dn -p

$ x joint -up -p ! it will generate test.injoint 

$ vi test.injoint !  set plasma frequency keep as it is as default

$ x joint -up -p

$ x joint -dn -p

$ x kram 

$ vi test.inkram ! set proper columns level and band transition 0 for semiconductor and 1 for metal

$ x kram 

$ opticplot_lapw 

$ save_lapw -optic -d pbe 




#### Anti-Ferromagnetic calculation

$ mkdir nio_afm

$ cd nio_afm

$ create supercell ! details to be come .......

$ cp supercell.struct NiO_afm.struct

$ x sgroup

$ vi NiO_amf.struct_sgroup

$ $vi NiO_afm.struct ! label two Ni as Ni1 and Ni2

$ x sgroup

$ vi NiO_afm.struct_sgroup

$ cp NiO_afm.struct_sgroup NiO_afm.struct

$ instgen_lapw -ask ! choose Ni1-u, Ni-d, O-n 

$ init_lapw -b -sp

$ runsp_lapw -p -ec 0.00001 -cc 0.0001 -NI

$ save_lapw -d pbe_afm


##### DOS #############
$ x lapw2 -qtl -up

$ x lapw2 -qtl -dn 

$ configure_int_lapw

$ x tetra -up 

$ x tetra -dn 

$ dosplot2 -up



############################################################################################

############################################################################################
################   By Pawan Joshi, M.Sc. Physics AMRL, CDP, TU (2077-2081)  ################
################             Github: https://github.com/pawan054            ################
############################################################################################

############################################################################################
