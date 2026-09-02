.. _ConcreteZBH_smoothed:

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

.. figure:: figures/ConcreteZBH_smoothed/stress_strain_models.png
   :align: center
   :width: 550

   Stress-strain behavior of the material.

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

.. note::

   **Credits & Contact**

   **Developers:**
   Prakash Singh Badal (Faculty, Civil Engineering, IIT Madras)
   Shingini Lahiri (Student, Civil Engineering, IIT Madras)

   **Contact:**
   Prof. Michele Barbato
   University of California, Davis
   mbarbato@ucdavis.edu

.. rubric:: References

* Zignago, D., Barbato, M., and Hu, D. (2018). "Constitutive Model of Concrete Simultaneously Confined by FRP and Steel for Finite-Element Analysis of FRP-Confined RC Columns." *Journal of Structural Engineering*, 144(10), 04018178. `https://doi.org/10.1061/(ASCE)ST.1943-541X.0002166 <https://doi.org/10.1061/(ASCE)ST.1943-541X.0002166>`_

* "New Analytical Analysis-Oriented Stress-Strain Model for FRP-and-Steel Confined Concrete." `https://doi.org/10.1061/JSENDH.STENG-11634 <https://doi.org/10.1061/JSENDH.STENG-11634>`_
