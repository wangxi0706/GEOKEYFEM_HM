# GEOKEYFEM_HM
The numerical simulation code of Geoinvention group (PI: Prof. Zhenyu YIN) of PolyU, mainly developed by Qi ZHANG.

## Functionality
The code simulates a contact problem between a rigid rectangular block with a Mohr-Coulomb soil by using the penalty method (**small deformation**). The deformation equation $\sigma_{ij,j} = 0$ is discretized  by using the [Smoothed Finite Element Method](https://www.taylorfrancis.com/books/mono/10.1201/EBK1439820278/smoothed-finite-element-methods-liu-nguyen-trung). The direct nodal integration on the smoothing domain is also modified to **stabilized conforming nodal integration (SCNI)**. This is reflected in this [file](./assemble_stab.m). The theory will be available upon the paper is accepted for publication.


Although the current example only considers the deformation field, the code is designed for hydromechanical coupling analysis (poromechanics). Therefore, the [pore pressure stabilization technique](https://doi.org/10.1016/j.cma.2008.05.015) is included. In addition, for simplicity, the biot coefficient $b$ is set to be `1` and Biot modulus $M$ is set to be `infinite`. It should be not too difficult to modify our code for a more general case by using relevant pre-defined matrices such as the `Mass matrix` $\int_{\Omega} N^T N \ {\rm d} V$ from this [file](./pre_assemble_MassN.m).


This is the [main file](./main_rigid_contact_prob.m). You can run it directly. In the main file, the most time-consuming part is the assembly process, whose function is given in this [file](./assemble_system.m). The [constitutive file](./MohrCoulomb_UMAT.m) is also called in the assembly process, as shown in the following code block:
```
[stress_new(:, ino), SDV_new(:, ino), cto] = MohrCoulomb_UMAT(0, Props(ino,:), stress(:, ino), strain_ino_new - strain_ino_old, SDV(:, ino));
```

This [file](./pre_assemble_BigK.m) *pre-aseembles* a **template** for the $2 \times 2$ block stiffness matrix. It makes the actual assemble [code](./assemble_system.m) more concise. However, it only works for constant biot coefficient/tensor and mobility, i.e., some material properties cannot change with location.


The [assign_tractionBC2](./assign_tractionBC2.m) is not used in this contact problem, while it is designed to calculate the equivalent nodal force vector $\int_{\Gamma} N^T \vec{t} \ {\rm d} A$ in FEM. The user only need to define the `traction_f` as a function of both location $x$ and time $t$ (P.S. the element wise multiplication `.*` should be adopted).


## Output
If you type `run main_rigid_contact_prob.m` in the MATLAB command window by using default parameters, you will get the following sample output. Five figures will also be generated:
- The undeformed mesh
- The deformed mesh
- The contour of horizontal and vertical displacements
- The contour of stress field $\sigma_{xx}$, $\sigma_{yy}$, $\tau_{xy}$, and $\sigma_{zz}$
- The contour of equivalent deviatoric plastic strain

```
LOAD STEP = 1; TIME = 8.64:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 1.2921E-10; RES = 5.6370E-05
LOAD STEP = 2; TIME = 17.28:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 2.4044E-10; RES = 1.0359E-04
LOAD STEP = 3; TIME = 25.92:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 1.7993E-04; RES = 1.4820E-04
LOAD STEP = 4; TIME = 34.56:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 3.7912E-03; RES = 1.8788E-04
	 NEWTON = 3; ERROR = 1.3685E-04; RES = 1.8766E-04
LOAD STEP = 5; TIME = 43.20:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 9.3549E-03; RES = 2.2662E-04
	 NEWTON = 3; ERROR = 7.9673E-04; RES = 2.2310E-04
LOAD STEP = 6; TIME = 51.84:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 2.3144E-02; RES = 2.5181E-04
	 NEWTON = 3; ERROR = 3.9380E-03; RES = 2.4277E-04
	 NEWTON = 4; ERROR = 8.6805E-04; RES = 2.4141E-04
LOAD STEP = 7; TIME = 60.48:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 4.0250E-02; RES = 2.6959E-04
	 NEWTON = 3; ERROR = 1.0140E-02; RES = 2.5473E-04
	 NEWTON = 4; ERROR = 2.8344E-03; RES = 2.5249E-04
	 NEWTON = 5; ERROR = 7.6317E-04; RES = 2.5204E-04
LOAD STEP = 8; TIME = 69.12:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 5.6692E-02; RES = 2.7744E-04
	 NEWTON = 3; ERROR = 2.0020E-02; RES = 2.5996E-04
	 NEWTON = 4; ERROR = 7.0351E-03; RES = 2.5681E-04
	 NEWTON = 5; ERROR = 2.4637E-03; RES = 2.5616E-04
	 NEWTON = 6; ERROR = 9.2800E-04; RES = 2.5595E-04
LOAD STEP = 9; TIME = 77.76:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 1.2716E-01; RES = 2.9601E-04
	 NEWTON = 3; ERROR = 4.6294E-02; RES = 2.6010E-04
	 NEWTON = 4; ERROR = 1.9707E-02; RES = 2.5343E-04
	 NEWTON = 5; ERROR = 7.3479E-03; RES = 2.5172E-04
	 NEWTON = 6; ERROR = 3.2769E-03; RES = 2.5123E-04
	 NEWTON = 7; ERROR = 1.3195E-03; RES = 2.5108E-04
	 NEWTON = 8; ERROR = 6.0660E-04; RES = 2.5103E-04
LOAD STEP = 10; TIME = 86.40:
	 NEWTON = 1; ERROR = 1.0000E+00; RES = 3.0000E+06
	 NEWTON = 2; ERROR = 2.0702E-01; RES = 2.8667E-04
	 NEWTON = 3; ERROR = 8.3940E-02; RES = 2.5344E-04
	 NEWTON = 4; ERROR = 3.6587E-02; RES = 2.4616E-04
	 NEWTON = 5; ERROR = 1.5123E-02; RES = 2.4407E-04
	 NEWTON = 6; ERROR = 6.9927E-03; RES = 2.4348E-04
	 NEWTON = 7; ERROR = 3.1249E-03; RES = 2.4328E-04
	 NEWTON = 8; ERROR = 1.5108E-03; RES = 2.4321E-04
	 NEWTON = 9; ERROR = 7.1468E-04; RES = 2.4318E-04
```