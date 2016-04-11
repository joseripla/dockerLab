http://aruizca.com/steps-to-create-a-vagrant-base-box-with-ubuntu-14-04-desktop-gui-and-virtualbox/

https://github.com/ckan/ckan/wiki/How-to-Create-a-CentOS-Vagrant-Base-Box


vagrant package --output ubuntuDevops.box --base ubuntuDevops
==> ubuntuDevops: Exporting VM...
==> ubuntuDevops: Compressing package to: /Users/erasmodominguezjimenez/Public/Vagrants/ubu/ubuntuDevops.box

vagrant box add ubuntuDevops ubuntuDevops.box 

vagrant box list 
vagrant init ubuntuDevops
vagrant up

******************************

https://www.vagrantup.com/docs/push/atlas.html

alta en atlas
Crear el repo 
vagrant login
Editar el vagrantFile con la confi ver --> 
config.push.define "atlas" do |push|
  push.app = "username/application"
end


Realmente no es necesario. Simplemente hay que subir el box http://www.sitepoint.com/create-share-vagrant-base-box/
