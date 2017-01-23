hotdoc_p = find_program('hotdoc', required : false)
has_hotdoc_extensions = false
hotdoc_extension_dir = []
if gst_dep.type_name() == 'internal'
    gst_proj = subproject('gstreamer')
    has_hotdoc_extensions = gst_proj.get_variable('build_hotdoc')
    if has_hotdoc_extensions
        hotdoc_extension_dir = [gst_proj.get_variable('hotdoc_extension_dir')]
    endif
else
    if hotdoc_p.found()
        cres = run_command(hotdoc_p, '--has-extension',
                'gst-extension', '--has-extension', 'gi-extension',
                '--has-extension', 'c-extension')
        if cres.returncode() == 0
            has_hotdoc_extensions = true
        else
            message('Hotdoc has extension output: ' + cres.stdout() + cres.stderr())
        endif
    endif
endif


build_hotdoc = false
if build_machine.system() == 'windows'
    message('Disabling documentation while building on Windows')
elif get_option('disable_doc')
    message('Documentation is disabled via options')
elif not hotdoc_p.found()
    message('Hotdoc not found, not building the documentation')
elif not build_gir
    message('GObject introspection not built, not building documentation')
elif not has_hotdoc_extensions
    message('Hotdoc GStreamer plugin scanner extension not found, not building' +
        'the documentation')
else
  build_hotdoc = true
  subdir('hotdoc')
endif
