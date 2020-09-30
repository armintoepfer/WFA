project(
  'wfa',
  'c',
  version : '1.0',
  default_options : [
    'buildtype=release',
    'warning_level=3',
    'c_std=c11',
    'b_ndebug=if-release'],
  license : 'MIT',
  meson_version : '>= 0.52.1')

##########
# CFLAGS #
##########

wfa_flags = ['-DHAVE_KALLOC']
c = meson.get_compiler('c')
foreach cflag: [
  '-Wc++-compat',
  '-Wno-unused-parameter',
  '-Wno-unknown-pragmas',
  '-Wno-sign-compare',
  '-Wno-pointer-arith',
]
  if c.has_argument(cflag)
    wfa_flags += cflag
  endif
endforeach

################
# dependencies #
################

# threads
wfa_thread_dep = dependency('threads', required : true)

# math
wfa_math_dep = c.find_library('m')

wfa_deps = [wfa_thread_dep, wfa_math_dep]

###########
# headers #
###########

if not meson.is_subproject()
  install_headers(
    files([
      'edit/edit_cigar.h',
      'edit/edit_dp.h',
      'edit/edit_table.h',
    ]),
    subdir : 'edit')

  install_headers(
    files([
      'gap_affine/affine_penalties.h',
      'gap_affine/affine_table.h',
      'gap_affine/affine_wavefront.h',
      'gap_affine/affine_wavefront_align.h',
      'gap_affine/affine_wavefront_backtrace.h',
      'gap_affine/affine_wavefront_display.h',
      'gap_affine/affine_wavefront_extend.h',
      'gap_affine/affine_wavefront_penalties.h',
      'gap_affine/affine_wavefront_reduction.h',
      'gap_affine/affine_wavefront_utils.h',
      'gap_affine/swg.h',
      'gap_affine/wavefront_stats.h',
    ]),
    subdir : 'gap_affine')

  install_headers(
    files([
      'gap_lineal/lineal_penalties.h',
      'gap_lineal/nw.h',
    ]),
    subdir : 'gap_lineal')

  install_headers(
    files([
      'system/mm_allocator.h',
      'system/profiler_counter.h',
      'system/profiler_timer.h',
    ]),
    subdir : 'system')

  install_headers(
    files([
      'utils/commons.h',
      'utils/dna_text.h',
      'utils/string_padded.h',
      'utils/vector.h',
    ]),
    subdir : 'utils')

endif

wfa_include_directories = include_directories('.')

#############
# libraries #
#############

# install library if
# - either running as a proper project
# - or using shared libraries
wfa_lib_install = (not meson.is_subproject()) or (get_option('default_library') == 'shared')

wfa_cpp_sources = files([
  'edit/edit_cigar.c',
  'edit/edit_dp.c',
  'edit/edit_table.c',
  'gap_affine/affine_penalties.c',
  'gap_affine/affine_table.c',
  'gap_affine/affine_wavefront.c',
  'gap_affine/affine_wavefront_align.c',
  'gap_affine/affine_wavefront_backtrace.c',
  'gap_affine/affine_wavefront_display.c',
  'gap_affine/affine_wavefront_extend.c',
  'gap_affine/affine_wavefront_penalties.c',
  'gap_affine/affine_wavefront_reduction.c',
  'gap_affine/affine_wavefront_utils.c',
  'gap_affine/swg.c',
  'gap_affine/wavefront_stats.c',
  'gap_lineal/nw.c',
  'system/mm_allocator.c',
  'system/profiler_counter.c',
  'system/profiler_timer.c',
  'utils/commons.c',
  'utils/dna_text.c',
  'utils/string_padded.c',
  'utils/vector.c',
])


wfa_bench_cpp_sources = files([
  'benchmark/benchmark_edit.c',
  'benchmark/benchmark_gap_affine.c',
  'benchmark/benchmark_gap_lineal.c',
  'benchmark/benchmark_utils.c',
])


wfa_lib = library(
  'wfa',
  wfa_cpp_sources,
  install : wfa_lib_install,
  # use boost SONAME practice:
  #   cause ld startup issues before
  #   you even have the chance of running
  #   into ABI issues.
  soversion : meson.project_version(),
  version : meson.project_version(),
  dependencies : wfa_deps,
  include_directories : wfa_include_directories,
  c_args : wfa_flags)

wfa_benchmark_lib = library(
  'wfa_bench',
  wfa_bench_cpp_sources,
  install : wfa_lib_install,
  # use boost SONAME practice:
  #   cause ld startup issues before
  #   you even have the chance of running
  #   into ABI issues.
  soversion : meson.project_version(),
  version : meson.project_version(),
  dependencies : wfa_deps,
  link_with: wfa_lib,
  include_directories : wfa_include_directories,
  c_args : wfa_flags)

###############
# executables #
###############

if not meson.is_subproject()
  wfa_adapt = executable(
    'wfa_adapt', files([
      'tools/examples/wfa_adapt.c']),
    install : not meson.is_subproject(),
    dependencies : wfa_deps,
    include_directories : wfa_include_directories,
    link_with : wfa_lib,
    c_args : wfa_flags)

  wfa_basic = executable(
    'wfa_basic', files([
      'tools/examples/wfa_basic.c']),
    install : not meson.is_subproject(),
    dependencies : wfa_deps,
    include_directories : wfa_include_directories,
    link_with : wfa_lib,
    c_args : wfa_flags)

  wfa_repeated = executable(
    'wfa_repeated', files([
      'tools/examples/wfa_repeated.c']),
    install : not meson.is_subproject(),
    dependencies : wfa_deps,
    include_directories : wfa_include_directories,
    link_with : wfa_lib,
    c_args : wfa_flags)

  align_benchmark = executable(
    'align_benchmark', files([
      'tools/align_benchmark.c']),
    install : not meson.is_subproject(),
    dependencies : wfa_deps,
    include_directories : wfa_include_directories,
    link_with : [wfa_lib, wfa_benchmark_lib],
    c_args : wfa_flags)

  generate_dataset = executable(
    'generate_dataset', files([
      'tools/generate_dataset.c']),
    install : not meson.is_subproject(),
    dependencies : wfa_deps,
    include_directories : wfa_include_directories,
    link_with : wfa_lib,
    c_args : wfa_flags)
endif

###################
# dependency info #
###################

if not meson.is_subproject()
  import('pkgconfig').generate(
    libraries : wfa_lib,
    version : meson.project_version(),
    name : 'wfa',
    filebase : 'wfa',
    description : 'Wavefront alignment algorithm (WFA): Fast and exact gap-affine pairwise alignment')
endif

wfa_dep = declare_dependency(
  include_directories : wfa_include_directories,
  link_with : wfa_lib,
  dependencies : wfa_deps,
  version : meson.project_version())
