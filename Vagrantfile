Vagrant.configure("2") do |config|
  config.vm.box     = "m3sserschmitt/aenigma5"
  config.vm.box_url = "https://boxes.aenigma.ro/metadata.json"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus   = 2
  end

  config.vm.provider "libvirt" do |lv|
    lv.memory = 2048
    lv.cpus   = 2
  end
end
