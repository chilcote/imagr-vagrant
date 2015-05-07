##imagr-vagrant

This vagrant environment (forked from [here](https://github.com/grahamgilbert/docker-vagrant)) will spin up an Ubuntu instance and load the necessary docker containers to test [Imagr](https://github.com/grahamgilbert/imagr) and [BSDPy](https://bitbucket.org/bruienne/bsdpy). 

##Requirements
+ VMware Fusion 7.x Professional
+ [Vagrant](https://www.vagrantup.com/downloads.html) 1.7.2+
+ [Vagrant VMware Provider](https://www.vagrantup.com/vmware) (purchase required)

##Instructions

Clone this repo and change to the nested `nbi` directory.

    git clone git@github.com:chilcote/imagr-vagrant.git && cd imagr-vagrant/nbi

Copy all images, packages, and scripts that used by the Imagr workflows to the `nbi` directory.

    $ ls .
    COSXIP.dmg OSX10.10.3_14D136_201505011226.hfs.dmg MunkiTools.pkg

Create an [imagr workflow](https://github.com/grahamgilbert/imagr/wiki/Workflow-Config) file.  Here's a really short example:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>password</key>
      <string>b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86</string>
      <key>workflows</key>
      <array>
        <dict>
          <key>name</key>
          <string>Deploy Munki</string>
          <key>description</key>
          <string>Deploys MunkiTools</string>
          <key>components</key>
          <array>
            <dict>
                <key>type</key>
                <string>package</string>
                <key>url</key>
                <string>http://localhost/MunkiTools.pkg</string>
            </dict>
          </array>
        </dict>
      </array>
    </dict>
    </plist> 

From the `nbi` directory, start a simple webserver. This is to share out the `imagr_config.plist` file that Imagr will load.

    python -m SimpleHTTPServer 8000

Open a new terminal session and clone the Imagr repo.

    git clone git@github.com:grahamgilbert/imagr.git && cd imagr

Create a [makefile override](https://github.com/grahamgilbert/imagr/wiki/Makefile-Override) with your defaults. Make sure to include your Mac's IP for the `imagr_config.plist` URL, and edit the other variables as necessary. Example:

    URL="http://10.0.0.2:8000/imagr_config.plist"
    APP="/Applications/Install OS X Yosemite.app"
    OUTPUT=~/Desktop/imagr-vagrant/nbi
    NBI="Imagr"
    ARGS="-e -p"
    BUILD=Testing

Create the custom nbi.

    make nbi

When this finishes, there should exist an `Imagr.nbi` build in the `nbi` directory.

    $ ls nbi/
    COSXIP.dmg OSX10.10.3_14D136_201505011226.hfs.dmg MunkiTools.pkg Imagr.nbi

If you are planning to test with a VMware Fusion OS X guest, edit the `NBImageInfo.plist` file to include `VMware7,1` in the `EnabledSystemIdentifiers` array.

    sudo vi nbi/Imagr.nbi/NBImageInfo.plist

From the `imagr-vagrant` directory, launch the virtual machine.

    vagrant up

It will show the startup.sh progress. When complete, you should have a working netboot server. You can view the BSDPy logs from inside the virtual machine.

    vagrant ssh
    sudo docker logs -f bsdpy    

###Client Testing

In VMware Fusion, create a new virtual machine by choosing `More Options` and `Create a custom virtual machine.` Customize the settings by changing the Network Adapter to `Autodetect`. Load your virtual machine, and it should netboot from your BSDPy instance and display the Imagr app.