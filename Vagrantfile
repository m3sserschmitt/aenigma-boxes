Vagrant.configure("2") do |config|
  config.vm.box     = "m3sserschmitt/aenigma5"
  config.vm.box_url = "https://boxes.aenigma.ro/metadata.json"
  config.vm.define  "aenigma5"

  config.vm.provider "virtualbox" do |vb|
    vb.name   = "aenigma5"
    vb.memory = 2048
    vb.cpus   = 2
  end

  config.vm.provider "libvirt" do |lv|
    lv.default_prefix = ""
    lv.memory = 2048
    lv.cpus   = 2
  end
end
