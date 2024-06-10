# demo-helm-library
Demo helm library for templates

## What is library chart

[official docs](https://helm.sh/docs/topics/library_charts/)

> **⚠️Warning:** Please read this first

Here is a simple example of a library chart based on the official Helm docs. In my opinion, using functions like `demo-library.util.merge` may be risky and unnecessary in the long run. Proceed with caution.

For my production solution, I will develop a distinct version of the library, relying on common sense and straightforward templates, avoiding Go template merging or similar techniques.

## OCI registry

You need create your oci registry, for example, use
[quay.io docs](https://access.redhat.com/documentation/en-us/red_hat_quay/3/html/about_quay_io/oci-intro)

Set your quay.io username and password:

1. Go to [https://quay.io/repository](https://quay.io/repository/)
2. Login
3. In the right top corner click at your name `Account Settings`
4. Click on link: `CLI Password:	Generate Encrypted Password`
5. Input your password
6. You get 2 values `Username & Encrypted Password`
7. Save them and add to your shell envs:

```bash
export QUAY_USERNAME=''
export QUAY_PASSWD_ENCRYPTED=''
```

8. Now you can login in your registry

```bash
helm registry login -u $QUAY_USERNAME -p $QUAY_PASSWD_ENCRYPTED quay.io
```

## Package helm library chart

Assuming your chart is in the current directory. This creates an archive with versions specified in `Chart.yaml`

```bash
helm package .
```

example:

```bash
$ helm package .
Successfully packaged chart and saved it to: /path/to/demo-helm-library/demo-library-0.1.2.tgz

# Chart.yaml inside the package has been created with the version from the current Chart.yaml:
$ tar -qxvf demo-library-0.1.2.tgz && cat demo-library/Chart.yaml | grep version
version: 0.1.2
```

Also, we can set the chart version manually. This may be useful if you will test some features locally.

```bash
helm package -u --version 0.1.2 .
```

example:

```bash
$ helm package -u --version 0.1.2 .
Successfully packaged chart and saved it to: /path/to/demo-helm-library/demo-library-0.1.2.tgz

# Chart.yaml inside the package has been created with the bumped version:
$ tar -qxvf demo-library-0.1.2.tgz && cat demo-library/Chart.yaml | grep version
version: 0.1.2
```

## Push chart to oci repo

```bash
helm push demo-library-0.1.2.tgz oci://quay.io/$QUAY_USERNAME/test-helm
```

example:
```bash
$ helm push demo-library-0.1.2.tgz oci://quay.io/$QUAY_USERNAME/test-helm
Pushed: quay.io/$QUAY_USERNAME/test-helm/demo-library:0.1.2
Digest: sha256:ae96270ed2cc592b52550a289dd82650ee6b39428a4befde721d58c20531aa5c
```

## Additional info

This helm library is used in my demo chart in another repository.This helm library used in my demo chart in other [repo](https://github.com/ksemele/demo-helm-chart)

If you are creating a CI/CD pipeline or doing manual builds, don't forget to delete archives after a successful push in the registry.
