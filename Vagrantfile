Vagrant.configure("2") do |config|
  config.vm.provider :docker do |docker, override|
    docker.image = "alvistack/gitlab-ee-18.0"
    docker.pull = true

    override.vm.synced_folder "./", "/vagrant"
  end
end
