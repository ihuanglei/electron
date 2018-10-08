vars = {
  'chromium_version': '68.0.3440.128',
  'node_version': '18a9880b70039f5d41ee860a95fe84e5ef928973',

  'boto_version': 'f7574aa6cc2c819430c1f05e9a1a1a666ef8169b',
  'pyyaml_version': '3.12',
  'requests_version': 'e4d59bedfd3c7f4f254f4f5d036587bcd8152458',

  'boto_git': 'https://github.com/boto',
  'chromium_git': 'https://chromium.googlesource.com',
  'electron_git': 'https://github.com/electron',
  'requests_git': 'https://github.com/kennethreitz',
  'yaml_git': 'https://github.com/yaml',

  # Python interface to Amazon Web Services. Is used for releases only.
  'checkout_boto': False,

  'checkout_nacl': False,
  'checkout_libaom': True,
  'checkout_oculus_sdk': False,

  # Python "requests" module is used for releases only.
  'checkout_requests': False,

  # It is always needed for normal Electron builds,
  # but might be impossible for custom in-house builds.
  'download_external_binaries': True,
}

deps = {
  'src':
    (Var("chromium_git")) + '/chromium/src.git@' + (Var("chromium_version")),
  'src/third_party/electron_node':
    (Var("electron_git")) + '/node.git@' + (Var("node_version")),
  'src/electron/vendor/pyyaml':
    (Var("yaml_git")) + '/pyyaml.git@' + (Var("pyyaml_version")),
  'src/electron/vendor/boto': {
    'url': Var('boto_git') + '/boto.git' + '@' +  Var('boto_version'),
    'condition': 'checkout_boto',
  },
  'src/electron/vendor/requests': {
    'url': Var('requests_git') + '/requests.git' + '@' +  Var('requests_version'),
    'condition': 'checkout_requests',
  },
}

hooks = [
  {
    'name': 'patch_chromium',
    'pattern': 'src/electron',
    'action': [
      'python',
      'src/electron/script/apply-patches',
      '--project-root=.',
      '--commit',
    ],
  },
  {
    'name': 'electron_external_binaries',
    'pattern': 'src/electron/script/update-external-binaries.py',
    'condition': 'download_external_binaries',
    'action': [
      'python',
      'src/electron/script/update-external-binaries.py',
      '--root-url=http://github.com/electron/electron-frameworks/releases/download',
      '--version=v1.4.0',
    ],
  },
  {
    'name': 'electron_npm_deps',
    'pattern': 'src/electron/package.json',
    'action': [
      'python',
      '-c',
      'import os; os.chdir("src"); os.chdir("electron"); os.system("npm install")',
    ],
  },
  {
    'name': 'setup_boto',
    'pattern': 'src/electron',
    'condition': 'checkout_boto',
    'action': [
      'python',
      '-c',
      'import os; os.chdir("src"); os.chdir("electron"); os.chdir("vendor"); os.chdir("boto"); os.system("python setup.py build");',
    ],
  },
  {
    'name': 'setup_requests',
    'pattern': 'src/electron',
    'condition': 'checkout_requests',
    'action': [
      'python',
      '-c',
      'import os; os.chdir("src"); os.chdir("electron"); os.chdir("vendor"); os.chdir("requests"); os.system("python setup.py build");',
    ],
  }
]

recursedeps = [
  'src',
]

gclient_gn_args = [
  'checkout_libaom',
  'checkout_nacl',
  'checkout_oculus_sdk',
]
gclient_gn_args_file =  'src/build/config/gclient_args.gni'
