hotdoc = import('hotdoc')

docconf = configuration_data()
docconf.set('GST_API_VERSION', api_version)

version_entities = configure_file(input : 'gst_api_version.in',
    output : 'gst_api_version.md',
    configuration : docconf)

html_theme = 'https://github.com/thiblahute/hotdoc-gnome-theme/releases/download/0.2/hotdoc-gnome-theme-0.2.tar.xz?sha256=3f14c75686403e592c4088187207ec3f57c023b12a8de2cc40f04274bb5bcafe'
excludes = []
foreach h: ['pbutils-private.h', 'gsttageditingprivate.h', 'id3v2.h',
  'kiss_fft_f32.h', 'kiss_fft_f64.h', 'kiss_fftr_f32.h', 'kiss_fftr_f64.h',
  'kiss_fftr_s16.h', 'kiss_fftr_s32.h', 'kiss_fft_s16.h', 'kiss_fft_s32.h',
  '_kiss_fft_guts_f32.h', '_kiss_fft_guts_f64.h', '_kiss_fft_guts_s16.h',
  '_kiss_fft_guts_s16.h', '_kiss_fft_guts_s32.h', '_kiss_fft_guts_s32.h',
  'pbutils-marshal.h', 'audio-resampler-private.h', '*orc-dist.[ch]',
  '*-neon.h', 'audio-resampler-macros.[ch]']

    excludes += [join_paths(meson.current_source_dir(), '..', '..', 'gst-libs/gst/*/', h)]
endforeach

libs = [
  ['allocators', allocators_gir, allocators_dep],
  ['app', app_gir, app_dep],
  ['audio', audio_gir, audio_dep],
  # FIXME! ['fft', fft_gir, fft_dep],
  ['pbutils', pbutils_gir, pbutils_dep],
  ['rtp', rtp_gir, rtp_dep],
  ['rtsp', rtsp_gir, rtsp_dep],
  ['sdp', sdp_gir, sdp_dep],
  ['tag', tag_gir, tag_dep],
  ['video', video_gir, video_dep],
]

libs_doc = []
foreach lib: libs
  name = lib[0]
  gir = lib[1]
  deps = [lib[2], gir]
  libs_doc += [hotdoc.generate_doc('gst-plugins-base-' + name,
      project_version: api_version,
      gi_c_sources: ['../../gst-libs/gst/' + name + '/*.[hc]'],
      gi_sources: gir[0].full_path(),
      gi_c_source_filters: excludes,
      sitemap: 'libs/' + name + '/sitemap.txt',
      index: 'libs/' + name + '/index.md',
      gi_index: 'libs/' + name + '/index.md',
      gi_smart_index: true,
      gi_order_generated_subpages: true,
      dependencies: deps,
      install: false,
      html_theme: html_theme,
  )]
endforeach

libs_doc += [hotdoc.generate_doc('gst-plugins-base-riff',
    project_version: api_version,
    c_sources: ['../../gst-libs/gst/riff/*.[hc]'],
    c_source_filters: excludes,
    sitemap: 'libs/riff/sitemap.txt',
    index: 'libs/riff/index.md',
    c_index: 'libs/riff/index.md',
    c_smart_index: true,
    c_order_generated_subpages: true,
    dependencies: [gst_base_dep, riff_dep],
    install: false,
    disable_incremental_build: true,
    html_theme: html_theme,
)]

base_libs_doc = hotdoc.generate_doc('gst-plugins-base-libs',
    project_version: api_version,
    sitemap: 'libs/sitemap.txt',
    index: 'libs/index.md',
    subprojects: libs_doc,
    disable_incremental_build: true,
    install: not get_option('disable_install_doc'),
    html_theme: html_theme,
)

plugins_doc = []
sitemap = 'all_index.md\n'
foreach l: plugins_component.targets()
    tname = l.full_path().split('/')[-1]
    if not tname.contains('.h') and not tname.contains('.c')
        plugins_doc += [hotdoc.generate_doc(tname,
            project_version: api_version,
            sitemap: 'plugins/sitemap.txt',
            index: 'plugins/index.md',
            gst_index: 'plugins/index.md',
            gst_smart_index: true,
            gst_c_sources: ['../../sys/*/*.[ch]',
                            '../../ext/*/*.[ch]',
                            '../../gst/*/*.[ch]',
                            ],
            gst_dl_sources: l.full_path(),
            dependencies: [gst_dep, l],
            gst_order_generated_subpages: true,
            extra_extensions_paths: hotdoc_extension_dir,
            install: false,
            disable_incremental_build: true,
            gst_cache_file: join_paths(meson.current_source_dir(), 'plugins', 'cache.json'),
            gst_plugin_name: tname,
            html_theme: html_theme,
        )]
        sitemap += '        @0@-doc.json\n'.format(tname)
    endif
endforeach

plugins_sitemap = configure_file(command: [python3, '-c', '''import os
os.chdir("''' + meson.current_source_dir() + '''")
with open("@OUTPUT@", "w") as f:
    f.write("""''' + sitemap + '""")'],
    input: 'sitemap.txt',
    output: 'sitemap.txt')

plugins = hotdoc.generate_doc('gst-plugins-base-plugins',
    project_version: api_version,
    sitemap: plugins_sitemap,
    index: 'plugins/all_index.md',
    subprojects: plugins_doc,
    disable_incremental_build: true,
    install: not get_option('disable_install_doc'),
    html_theme: html_theme,
)

doc = hotdoc.generate_doc('gst-plugins-base',
    project_version: api_version,
    sitemap: 'sitemap.txt',
    index: 'index.md',
    subprojects: [base_libs_doc, plugins],
    disable_incremental_build: true,
    install: not get_option('disable_install_doc'),
    html_theme: html_theme,
)
