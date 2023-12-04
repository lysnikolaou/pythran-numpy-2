project('pyhtran-numpy-2', 'c', 'cpp')

py_mod = import('python')
py3 = py_mod.find_installation(pure: false)
py3_dep = py3.dependency()

cc = meson.get_compiler('c')
cpp = meson.get_compiler('cpp')

# NumPy include directory
incdir_numpy = meson.get_external_property('numpy-include-dir', 'not-given')
if incdir_numpy == 'not-given'
  # If not specified, try to query NumPy from the build python
  incdir_numpy = run_command(py3,
    [
      '-c',
      'import os; os.chdir(".."); import numpy; print(numpy.get_include())'
    ],
    check: true
  ).stdout().strip()
endif

# Pythran include directory
pythran = find_program('pythran')
incdir_pythran = meson.get_external_property('pythran-include-dir', 'not-given')
if incdir_pythran == 'not-given'
  # If not specified, try to query Pythran from the build python
  incdir_pythran = run_command(py3,
    [
      '-c',
      'import os; os.chdir(".."); import pythran; print(os.path.dirname(pythran.__file__));'
    ],
    check: true
  ).stdout().strip()
endif

_brief_pythran = custom_target('_brief_pythran',
    output: ['brief_cy.cpp'],
    input: 'brief_pythran.py',
    command: [pythran, '-E', '@INPUT@', '-o', '@OUTPUT@']
)

# Pythran build flags
cpp_args_pythran = [
  '-DENABLE_PYTHON_MODULE',
  '-D__PYTHRAN__=3',
  '-DPYTHRAN_BLAS_NONE'
]

py3.extension_module('brief_cy',
    [_brief_pythran],
    cpp_args: cpp_args_pythran,
    include_directories: [incdir_numpy, incdir_pythran],
    dependencies: [py3_dep],
)
