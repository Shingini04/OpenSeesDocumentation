.. _ConcreteZBH:

ConcreteZBH - FRP- and steel-confined concrete Material
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command constructs a uniaxial confined concrete material with cyclic unloading and reloading rules.
The model includes confinement from transverse steel and FRP, iterative confinement pressure calculation,
a nonlinear compression envelope, and tension cut-off behavior.

.. function:: uniaxialMaterial ConcreteZBH_smoothed $matTag $fc0 $ec0 $Ec $Es $fy $eults $s $As_t $Ef $eultf $tf $D $Ds $As_l $kg_f $ks_s $ks_f $type_reinf

.. csv-table::
   :header: "Argument", "Type", "Description"
   :widths: 10, 10, 40

   $matTag, |integer|, Material tag
   $fc0, |float|, Unconfined concrete compressive strength (negative)
   $ec0, |float|, Strain at peak unconfined strength (negative)
   $Ec, |float|, Elastic modulus of concrete
   $Es, |float|, Elastic modulus of transverse steel
   $fy, |float|, Steel yield strength
   $eults, |float|, Ultimate steel strain
   $s, |float|, Spacing of transverse reinforcement
   $As_t, |float|, Area of transverse reinforcement
   $Ef, |float|, Elastic modulus of FRP
   $eultf, |float|, Ultimate FRP strain
   $tf, |float|, FRP thickness
   $D, |float|, Section diameter
   $Ds, |float|, Core diameter
   $As_l, |float|, Longitudinal reinforcement area
   $kg_f, |float|, FRP geometric coefficient
   $ks_s, |float|, Steel confinement coefficient
   $ks_f, |float|, FRP confinement coefficient
   $type_reinf, |float|, "Transverse reinforcement type parameter (e.g., 1.0 for circular spirals/hoops, 2.0 for rectangular/rectilinear ties)"

.. note::

   * Both **$fc0** (unconfined compressive strength) and **$ec0** (strain at peak unconfined strength) must be input as negative values (compression is negative).
   * Users must maintain consistent units across all inputs (e.g., N, mm, MPa or kips, in, ksi).
   * Tension stress is set to zero for positive strain (tension cut-off).
   * Confinement pressure is solved iteratively with a maximum of 20 iterations.

.. figure:: figures/ConcreteZBH/stress_strain_models.png
   :align: center
   :width: 550

   Stress-strain behavior of the material.
   Monotonic envelope of the stress-strain behavior.

.. figure:: figures/ConcreteZBH/cyclic_stress_strain.png
   :align: center
   :width: 550

   Cyclic stress-strain behavior for FRP + steel confinement.

.. admonition:: Example

   The following is used to construct a ``ConcreteZBH_smoothed`` material.

   .. code-block:: tcl

      set matTag      1
      set fc0         -53.61
      set ec0         -0.00246967424
      set Ec          36609.425
      set Es          200000.0
      set fy          437.0
      set eults       0.10
      set s           60.0
      set As_t        78.54
      set Ef          45000.0
      set eultf       0.015
      set tf          1.58
      set D           205.0
      set Ds          155.0
      set As_l        678.6
      set kg_f        1.0
      set ks_s        1.0
      set ks_f        1.0
      set type_reinf  2.0
      uniaxialMaterial ConcreteZBH_smoothed $matTag $fc0 $ec0 $Ec $Es $fy $eults $s $As_t $Ef $eultf $tf $D $Ds $As_l $kg_f $ks_s $ks_f $type_reinf

Structural Column Example (DB450-C)
"""""""""""""""""""""""""""""""""""
.. rubric:: Structural Column Example (DB450-C)

.. raw:: html

   <details>
   <summary><b>Click to expand OpenSees structural example code</b></summary>

.. code-block:: tcl

   # DB450-C concentric axial-compression model (N, mm, MPa)
   # Specimen DB450-C (Parretti and Nanni 2002; Zignago et al. 2018)

   wipe

   # Confinement case: "mSM" (combined FRP + steel confinement) or "SM" (FRP only)
   set confinementCase "mSM"

   model BasicBuilder -ndm 2 -ndf 3

   # Geometry and concrete properties
   set D 200.0
   set H 914.0
   set fc0 -25.5
   set epsc0 -0.0020;                 # Strain at peak unconfined compressive strength
   set Ec [expr 5000.0*sqrt(abs($fc0))]

   # Longitudinal steel reinforcement (8 #10 bars)
   set nBars 8
   set dBar 10.0
   set fyLong 393.0
   set EsLong 200000.0
   set bLong 0.005;                   # Monotonic Steel02 hardening ratio

   # Transverse steel reinforcement (spiral)
   set dSpiral 6.0
   set spiralPitch 50.0
   set fySpiral 517.0
   set EsSpiral 200000.0
   set epsSu 0.10

   # Cover and core dimensions
   set cover 20.0
   set Dcore [expr $D-2.0*$cover]

   set pi [expr acos(-1.0)]
   set AsBar [expr $pi*$dBar*$dBar/4.0]
   set AsLong [expr $nBars*$AsBar]
   set AsSpiral [expr $pi*$dSpiral*$dSpiral/4.0]

   # FRP jacket properties
   set tFRP 0.270
   set EFRP 125600.0
   set epsFRPcoupon 0.019
   set xiFRP 0.45
   set epsFRPeffective [expr $xiFRP*$epsFRPcoupon]

   set matCore 1
   set matCover 2
   set matSteel 3

   if {$confinementCase eq "mSM"} {
       set coreSpiralFy $fySpiral
       set coreAsLong $AsLong
   } else {
       # SM case: FRP-only concrete; longitudinal bars remain explicit
       set coreSpiralFy 0.0
       set coreAsLong 0.0
   }

   # Uniaxial materials: ConcreteZBH for core and cover concrete
   # Arguments: tag fc0 epsc0 Ec Es fy epsSu pitch As_t Ef epsFRP tf D Dcore As_long kg_f ks_s ks_f type_reinf
   uniaxialMaterial ConcreteZBH_original $matCore $fc0 $epsc0 $Ec $EsSpiral $coreSpiralFy $epsSu $spiralPitch $AsSpiral $EFRP $epsFRPeffective $tFRP $D $Dcore $coreAsLong 1.0 1.0 1.0 1.0

   uniaxialMaterial ConcreteZBH_original $matCover $fc0 $epsc0 $Ec $EsSpiral 0.0 $epsSu $spiralPitch $AsSpiral $EFRP $epsFRPeffective $tFRP $D $Dcore 0.0 1.0 1.0 1.0 1.0

   uniaxialMaterial Steel02 $matSteel $fyLong $EsLong $bLong 20.0 0.925 0.15

   # Section discretization (Fiber section)
   set R [expr $D/2.0]
   set Rcore [expr $Dcore/2.0]
   set Rbar [expr $R-$cover-$dBar/2.0]

   section Fiber 1 {
       patch circ $matCore 20 16 0.0 0.0 0.0 $Rcore 0.0 360.0
       patch circ $matCover 20 4 0.0 0.0 $Rcore $R 0.0 360.0
       layer circ $matSteel $nBars $AsBar 0.0 0.0 $Rbar 0.0 315.0
   }

   # Nodes and boundary conditions
   node 1 0.0 0.0
   node 2 0.0 $H
   fix 1 1 1 1
   fix 2 1 0 1

   # Coordinate transformation and element definition
   geomTransf Linear 1
   element forceBeamColumn 1 1 2 1 Lobatto 1 5

   # Recorders
   recorder Node -file displacement.out -node 2 -dof 2 disp
   recorder Node -file reaction.out -node 1 -dof 2 reaction
   recorder Element -file core_fiber.out -ele 1 section 3 fiber 0.0 0.0 $matCore stressStrain
   recorder Element -file cover_fiber.out -ele 1 section 3 fiber 0.0 [expr 0.5*($Rcore+$R)] $matCover stressStrain

   # Loading and analysis definition
   timeSeries Linear 1
   pattern Plain 1 1 {load 2 0.0 -1.0 0.0}

   constraints Plain
   numberer Plain
   system BandGeneral
   test NormDispIncr 1.0e-8 50 0
   algorithm Newton
   set dU -0.002
   integrator DisplacementControl 2 2 $dU
   analysis Static

   # Displacement-controlled execution
   set targetDisp [expr -0.020*$H]
   set ok 0
   set steps 0
   while {[nodeDisp 2 2] > $targetDisp && $ok == 0} {
       set ok [analyze 1]
       if {$ok == 0} {incr steps}
   }

   puts "DB450-C ($confinementCase): return=$ok accepted_steps=$steps final_disp=[nodeDisp 2 2] mm"
   wipe

.. raw:: html

   </details>
   <br>

.. figure:: figures/ConcreteZBH/DB450C_structural_model.png
   :align: center
   :width: 600

   Description of the structural model and fiber-discretized section.

.. figure:: figures/ConcreteZBH/DB450C_fiber_response.png
   :align: center
   :width: 550

   Stress-strain response of the core and cover concrete fibers.

.. figure:: figures/ConcreteZBH/DB450C_axial_response.png
   :align: center
   :width: 550

   Global axial force-displacement response of the column.

.. note::

   **Credits & Contact**

   **Developers:**
   Shingini Lahiri (Student, Civil Engineering, IIT Madras)
   Prakash Singh Badal (Faculty, Civil Engineering, IIT Madras)
   Shingini Lahiri (Student, Civil Engineering, IIT Madras)
   Michele Barbato (Faculty, Civil & Environmental Engineering, UC Davis)

   **Contact:**
   Prof. Michele Barbato
   University of California, Davis
   mbarbato@ucdavis.edu

.. rubric:: References

* Zignago, D., Barbato, M., and Hu, D. (2018). "Constitutive Model of Concrete Simultaneously Confined by FRP and Steel for Finite-Element Analysis of FRP-Confined RC Columns." *Journal of Structural Engineering*, 144(10), 04018178. `https://doi.org/10.1061/(ASCE)ST.1943-541X.0002166 <https://doi.org/10.1061/(ASCE)ST.1943-541X.0002166>`_

* "New Analytical Analysis-Oriented Stress-Strain Model for FRP-and-Steel Confined Concrete." `https://doi.org/10.1061/JSENDH.STENG-11634 <https://doi.org/10.1061/JSENDH.STENG-11634>`_
* Zignago, D., and Barbato, M. (2023). "New Analytical Analysis-Oriented Stress-Strain Model for FRP-and-Steel Confined Concrete." *Journal of Structural Engineering*, 149(1), 04022212. `https://doi.org/10.1061/JSENDH.STENG-11634 <https://doi.org/10.1061/JSENDH.STENG-11634>`_ (Note: This paper contains both the smoothed and fitted models.)
