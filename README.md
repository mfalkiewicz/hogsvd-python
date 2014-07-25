hogsvd-python
=============



This script calculates the generalized SVD(GSVD) for N>2 matrices. This is referred to as the higher-order
GSVD(HOGSVD). This is based upon work done by Ponnapalli, Alter et al in the paper: 
Ponnapalli, Sri Priya, et al. "A higher-order generalized singular value decomposition for comparison of global mRNA expression 
from multiple organisms." PloS one 6.12 (2011): e28072.

Usage : "hogsvd.py -m textFileWithMatrixD1 -m textFileWithMatrixD2 -m textFileWithMatrixD3 -o outputDir"

Notes:

1.outputDir will contain the text files, with U and Sigma matrices for each of the input matrices specified by the -m switch. 
A single file in the output directory is created for the shared subspace V.

2. When only two matrices are specified, the QR-decomposition based algorithm is called from LAPACK.

3. When three or more matrices are specified the algorithm specified in the paper is called. This algorithm in *not* based on the
   QR decomposition.
   a. The algorithm is as follows(ref. Ponnapalli et al.):

      i. For each input matrix Di form Ai=Di^T*Di

      ii. Using all Ai,Aj pairs calculate the balanced sum Sij

      iii. Sum over all i,j ~ Si,j to get S

      iv. Do eigen-decomposition on S --> get V and L(Lambda).

      v. Solve a linear system of equations for each Di and V to get Bi

      vi. Normalize columns of Bi to get Ui, norm of the columns form the elements of elements of the diagonal matrix Sigma_i

4. To generate the sggsvd binding use f2py like this:

   Step 1: Generate Signature file:

   f2py ../lapack-3.5.0/SRC/sggsvd.f -m sggsvd2 -h sig.pyf

   Step 2: Modify sig.pyf to generate sig2.pyf (Trick is in declaring the input and output variables using input=intent(in),and output=intent(in)+intent(out)

   Step 3: Use sig2.pyf to generate the ssgsvd2 module

   f2py -L../lapack-3.5.0/liblapack.a -llapack -c sig2.pyf ../lapack-3.5.0/SRC/sggsvd.f

   Step 4: import ssgsvd2 in the python script as in hogsvd.py 