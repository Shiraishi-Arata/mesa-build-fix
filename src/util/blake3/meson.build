files_blake3 = [
    'blake3.c',
    'blake3_dispatch.c',
    'blake3_portable.c'
]
blake3_defs = []

is_windows = host_machine.system() == 'windows'
is_msvc = meson.get_compiler('c').get_id() == 'msvc'
cpu_family = host_machine.cpu_family()

blake3_x86_no_simd_defs = ['-DBLAKE3_NO_SSE2', '-DBLAKE3_NO_SSE41', '-DBLAKE3_NO_AVX2', '-DBLAKE3_NO_AVX512']

if cpu_family == 'x86_64'
  if is_windows
    if is_msvc
      if meson.backend() == 'ninja' and add_languages('masm', required : false)
        files_blake3 += ['blake3_sse2_x86-64_windows_msvc.masm', 'blake3_sse41_x86-64_windows_msvc.masm', 'blake3_avx2_x86-64_windows_msvc.masm', 'blake3_avx512_x86-64_windows_msvc.masm']
      else
        blake3_defs += blake3_x86_no_simd_defs
      endif
    else
      files_blake3 += ['blake3_sse2_x86-64_windows_gnu.S', 'blake3_sse41_x86-64_windows_gnu.S', 'blake3_avx2_x86-64_windows_gnu.S', 'blake3_avx512_x86-64_windows_gnu.S']
    endif
  elif cc.sizeof('void *') == 4
    blake3_defs += blake3_x86_no_simd_defs
  else
    files_blake3 += ['blake3_sse2_x86-64_unix.S', 'blake3_sse41_x86-64_unix.S', 'blake3_avx2_x86-64_unix.S', 'blake3_avx512_x86-64_unix.S']
  endif
elif cpu_family == 'x86'
  blake3_defs += blake3_x86_no_simd_defs
elif cc.get_define('__aarch64__') == '1' and target_machine.endian() == 'little'
  files_blake3 += ['blake3_neon.c']
endif

blake3 = static_library(
  'blake3',
  files_blake3,
  c_args : blake3_defs + cc.get_supported_arguments('-Wno-error'),
  gnu_symbol_visibility : 'hidden',
  build_by_default : false,
)

idep_blake3 = declare_dependency(
  link_with : blake3,
)
