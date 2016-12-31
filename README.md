# AOSP 7.1.0_r4 Manifest for hammerhead

Getting Started
---------------
To get started with the Minimal sources, you'll need to get
familiar with [Git and Repo](http://source.android.com/source/version-control.html).

Create the Directories
----------------------

You will need to set up some directories in your build environment.
First for repo tool and second for firmware build location.

To create them run:
```
mkdir -p ~/bin
mkdir -p ~/android_firmware
```

Install the Repository
----------------------

Enter the following to download the "repo" binary and make it executable:
```
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo && chmod a+x ~/bin/repo
```

You may need to reboot for these changes to take effect.
Now enter the following to initialize the repository:
```
cd ~/android_firmware
```

Repositories:
---------------

For initializing repo use:
```
repo init -u https://github.com/aquaxp-aosp/platform_manifest -b aosp-7.1
```

Syncing repo:

    repo sync --no-clone-bundle -j$(# of CPUs x2)


Initializing env

    source build/envsetup.sh

Choosing a target

    lunch


Building OTA zip, where N is number of jobs

    make otapackage -jN


Building images, where N is number of jobs

    make -jN

If you wanna release your firmware, you need to do this instead:

    make -jN dist
    
then sign files:

    export KEY_DIR=./your_path_to_dir_with_keys
    ./build/tools/releasetools/sign_target_file_apks -o -d $KEY_DIR out/dist/.*-target_files.*.zip /tmp/signed.zip

Generate relaese files with full system image:

    ./build/tools/releasetools/img_from_target_files /tmp/signed.zip /tmp/final-release.zip

or OTA package (also capable with custom recovery):

    ./build/tools/releasetools/ota_from_target_files /tmp/signed.zip /tmp/final-full-ota.zip

or Incremental OTA:

     ./build/tools/releasetools/ota_from_target_files -k $KEY_DIR/platform -i /tmp/last-signed.zip /tmp/signed.zip /tmp/final-full-ota.zip

How to sync with 3-rd party repos
------------------------------------

Setup some environment variables:
```
repo_name=repo_name
upstream_repo_server=upstream_server
my_repo_server=my_server
upstream_branch=ups_b
my_branch=my_b
```

And run sequence:
```
git clone ${my_repo_server}/${repo_name}
cd ${repo_name}
git remote add upstream ${upstream_repo_server}/${repo_name}
git fetch upstream
git checkout -b ${my_branch}  --track upstream/${upstream_branch}
git push origin ${my_branch}
cd ..
```

Trobleshooting
--------------
If you experience Jack compilations failing on `Out of memory error.`, stop the server using `jack-admin stop-server`, and then:
`export ANDROID_JACK_VM_ARGS="-Xmx4g -Dfile.encoding=UTF-8 -XX:+TieredCompilation"`
and restart your build command (hint from [Jack README](https://android.googlesource.com/platform/prebuilts/sdk/+/master/tools/README-jack-server.md#If-you-experience-Jack-compilations-failing-on)).

~~If you're using Ubuntu 16.04, you should apply this fix to kernel sources - https://gerrit.omnirom.org/#/c/12887/1/kernel/timeconst.pl~~
