# Configuration the gfortran compiler to work with Abaqus
TTo use GNU Fortran Compiler with Abaqus the compiler and linker options in Abauqs config file must be modified. The following options added to global (lnx86_64.env) or local (abaqus_v6.env) Abaqus configuration files enables to compile and to link correctly Abaqus user subroutines on Linux using gfortran:

```
import os

abaHomeInc = os.path.abspath(os.path.join(os.environ.get('ABA_HOME', ''), os.pardir))

fortCmd = "gfortran"

compile_fortran = [fortCmd,
                   '-c', '-cpp', '-fPIC','-extend-source',
                   '-DABQ_LNX86_64', '-DABQ_FORTRAN',
                   '-fautomatic',
                   '-mno-fma', 
                   '-fprotect-parens',
                   '-fstack-protector-strong',
                   '-msse3',
                   '-mavx', '-mavx2',
                   '-free',
                   '-I%I', '-I'+abaHomeInc, '%P']

link_sl = [fortCmd,
           '-fPIC', '-shared','-Wl,--add-needed', 
           '%E', '-Wl,-soname,%U', '-o', '%U', '%F', '%A', '%L', '%B',
           '-Wl,-Bdynamic']

link_exe = [fortCmd,
            '-fPIC',
            '-Wl,-Bdynamic', '-Wl,--add-needed', '-o', '%J', '%F', '%M', '%L', '%B', '%O']

del fortCmd
del abaHomeInc
```

The easiest way to set it up is to put the file provided abaqus_v6.env file to your home directory or directory where you run Abaqus with subroutines. If you have abaqus_v6.env already at that please copy the statements which define and delete variables to your abaqus_v6.env file.

To check if it works correctly run the command:
```
$ abaqus verify user_std user_exp make

------------------------------------------------------------

Abaqus Product Verification

Mon 17 Jan 2022 10:11:25 PM CET

------------------------------------------------------------

Verify test : Abaqus/Standard with user subroutines verification

     result : PASS

------------------------------------------------------------

Verify test : Abaqus/Explicit with user subroutines verification

  - 'Abaqus/Explicit single precision user subroutine' completed, validation Succeeded

  - 'Abaqus/Explicit double precision user subroutine' completed, validation Succeeded

     result : PASS

------------------------------------------------------------

Verify test : Abaqus make utility verification

  - 'Abaqus make C++ verification' completed, validation Succeeded

  - 'Abaqus make C++ PostProcess Application' completed, validation Succeeded

  - 'Abaqus make Fortran verification' completed, validation Succeeded

  - 'Abaqus make Fortran PostProcess Application' completed, validation Succeeded

  - 'Abaqus make object file verification' completed, validation Succeeded

  - 'Abaqus make object file PostProcess Application' completed, validation Succeeded

     result : PASS

------------------------------------------------------------

Verification procedure complete

Mon 17 Jan 2022 10:11:54 PM CET

------------------------------------------------------------

```

Happy coding!

References:
1. [The GNU Fortran Compiler]()
2. [A GNU Manual ]()
