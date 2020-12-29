# Pod registry

The central place to register [babashka pods](https://github.com/babashka/pods) for easy usage with babashka.

- [Pod registry](#pod-registry)
  - [Loading and using a pod](#loading-and-using-a-pod)
  - [Registered pods](#registered-pods)
  - [Registering a pod](#registering-a-pod)

## Loading and using a pod

Registered pods can be loaded using a qualified symbol and a version string:

``` clojure
(require '[babashka.pods :as pods])
(pods/load-pod 'org.babashka/buddy "0.0.1")
```

From then on, a pod exposes namespaces which can be called like regular Clojure:

``` clojure
(require '[pod.babashka.buddy.hash :as hash])
(hash/md5 "foo")
```

## Registered pods

| Pod      | Description           | Latest version  | Example    | Language |
| ------------- |-----------------------|-----------------|---------| -------|
| [borkdude/clj-kondo](https://github.com/borkdude/clj-kondo) | A clojure linter that sparks joy | 2020.12.12 | [link](examples/clj-kondo.clj) | Clojure |
| [justone/brisk](https://github.com/justone/brisk) | Freeze and thaw with Nippy | 0.2.0 | [link](examples/brisk.clj) | Clojure |
| [justone/tabl](https://github.com/justone/tabl)  | Make tables from data in your terminal | 0.2.0 | [link](examples/tabl.clj) | Clojure |
| [lispyclouds/docker](https://github.com/lispyclouds/pod-lispyclouds-docker) | Interact with Docker | 0.1.1 | [link](examples/docker.clj) | Clojure |
| [org.babashka/buddy](https://github.com/babashka/pod-babashka-buddy) | Cryptographic API provided by buddy | 0.0.1 | [link](examples/buddy.clj) | Clojure |
| [org.babashka/etaoin](https://github.com/babashka/pod-babashka-etaoin) | Etaoin, a pure Clojure webdriver protocol implementation  | 0.0.1 | [link](examples/etaoin.clj) | Clojure |
| [org.babashka/filewatcher](https://github.com/babashka/pod-babashka-filewatcher) | Filewatcher based on Rust notify | 0.0.1 | [link](examples/filewatcher.clj) | Rust |
| [org.babashka/fswatcher](https://github.com/babashka/pod-babashka-fswatcher) | Filewatcher based on Go fsnotify | 0.0.1 | [link](examples/fswatcher.clj) | Go |
| [org.babashka/hsqldb](https://github.com/babashka/babashka-sql-pods/) | HSQLDB access via next.jdbc | 0.0.1 | [link](examples/hsqldb.clj) | Clojure |
| [org.babashka/parcera](https://github.com/babashka/pod-babashka-parcera) | Grammar-based Clojure(script) parser | 0.0.1| [link](examples/parcera.clj) | Clojure |
| [org.babashka/postgresql](https://github.com/babashka/babashka-sql-pods/) | Postgresql access via next.jdbc | 0.0.1 | See hsqldb | Clojure |
| [retrogradeorbit/bootleg](https://github.com/retrogradeorbit/bootleg) | Simple template processing command line tool to help build static websites | 0.1.9 | [link](examples/bootleg.clj) | Clojure |
| [tzzh/aws](https://github.com/tzzh/pod-tzzh-aws) | Interact with AWS | 0.0.3 | [link](examples/tzzh_aws.clj) | Go |
| [tzzh/mail](https://github.com/tzzh/pod-tzzh-mail) | Send emails | 0.0.2 | [link](examples/tzzh_mail.clj) | Go |

## Registering a pod

To register a pod, create a nested directory in `manifests` with the following structure:

```
<org>/<pod-name>/<version>
```

and add a `manifest.edn` file like the following example:

``` clojure
{:pod/name tzzh/mail
 :pod/description ""
 :pod/version "0.0.2"
 :pod/license ""
 :pod/artifacts
 [{:os/name "Linux.*"
   :os/arch "amd64"
   :artifact/url "https://github.com/tzzh/pod-tzzh-mail/releases/download/v0.0.2/pod-tzzh-mail_0.0.2_Linux_x86_64.zip"
   :artifact/executable "pod-tzzh-mail"}
  {:os/name "Mac.*"
   :os/arch "x86_64"
   :artifact/url "https://github.com/tzzh/pod-tzzh-mail/releases/download/v0.0.2/pod-tzzh-mail_0.0.2_Darwin_x86_64.zip"
   :artifact/executable "pod-tzzh-mail"}
  {:os/name "Windows.*"
   :os/arch "amd64"
   :artifact/url "https://github.com/tzzh/pod-tzzh-mail/releases/download/v0.0.2/pod-tzzh-mail_0.0.2_Windows_x86_64.zip"
   :artifact/executable "pod-tzzh-mail.exe"}]}
```

The required fields are `:pod/name`, `:pod/version` and `:pod/artifacts`.

You can then load the pod in your babashka script as follows:

``` clojure
(require '[babashka.pods :as pods])
(pods/load-pod 'tzzh/mail "0.0.2")

(require '[pod.tzzh.mail :as m])
(m/send-mail ...)
```


The `:pod/artifacts` vector will be matched in order on operating system and
architecture. For the first match, the `:articact/url`, a zip file, will be
downloaded and extracted. After extraction there should be a file with the same
name as `:artifact/executable` which will be made executable and invoked as the
pod.

