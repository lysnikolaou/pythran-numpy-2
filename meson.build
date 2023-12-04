project('pyhtran-numpy-2', 'c', 'cpp', default_options: ['cpp_std=c++14'])

py_mod = import('python')
py3 = py_mod.find_installation(pure: false)
py3_dep = py3.dependency()

cc = meson.get_compiler('c')
cpp = meson.get_compiler('cpp')

incdir_numpy = meson.get_external_property('numpy-include-dir', 'not-given')
if incdir_numpy == 'not-given'
  incdir_numpy = run_command(py3,
    [
      '-c',
      '''import os
import numpy as np
try:
    incdir = os.path.relpath(np.get_include())
except Exception:
    incdir = np.get_include()
print(incdir)
'''
    ],
    check: true
  ).stdout().strip()

  # We do need an absolute path to feed to `cc.find_library` below
  _incdir_numpy_abs = run_command(py3,
    ['-c', 'import os; os.chdir(".."); import numpy; print(numpy.get_include())'],
    check: true
  ).stdout().strip()
else
  _incdir_numpy_abs = incdir_numpy
endif
inc_np = include_directories(incdir_numpy)
np_dep = declare_dependency(include_directories: inc_np)

pythran = find_program('pythran', native: true)
incdir_pythran = meson.get_external_property('pythran-include-dir', 'not-given')
if incdir_pythran == 'not-given'
incdir_pythran = run_command(py3,
    [
    '-c',
    '''import os
import pythran
try:
    incdir = os.path.relpath(pythran.get_include())
except Exception:
    incdir = pythran.get_include()
print(incdir)
'''
    ],
    check: true
).stdout().strip()
endif
pythran_dep = declare_dependency(include_directories: incdir_pythran)

brief = custom_target('brief',
    output: ['brief.cpp'],
    input: 'brief.py',
    command: [pythran, '-E', '@INPUT@', '-o', '@OUTDIR@/brief.cpp']
)

cpp_args_pythran = [
  '-DENABLE_PYTHON_MODULE',
  '-D__PYTHRAN__=3',
  '-DPYTHRAN_BLAS_NONE',
]

py3.extension_module('brief',
    brief,
    cpp_args: cpp_args_pythran,
    dependencies: [pythran_dep, np_dep],
    install: true,
)
