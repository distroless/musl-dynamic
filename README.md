# musl-dynamic

Distroless base image with just enough files to run binaries compiled against musl. This image
contains only the musl library, root certificate data and various commonly required files and
directories including `/etc/passwd` and `/tmp`.

This image is intended for running binaries that have been compiled against musl but do not include
the musl library. Binaries must have no other dependencies. (For completely static binaries see 
[distroless/static](https://github.com/distroless/static).

This image is regenerated nightly.

## Usage

Here's an example Dockerfile that builds a C binary against musl
and puts it into the musl-dynamic image:


```Dockerfile
FROM distroless.dev/gcc-musl as build

COPY hello.c /work/hello.c
RUN cc hello.c -o hello

FROM distroless.dev/musl-dynamic

COPY --from=build /work/hello /hello
CMD ["/hello"]
```
To build and run it:

```bash
$ docker build -t distroless-musl --file Dockerfile.c .
...
$ docker run distroless-musl
Hello Distroless
```

Note the size!

```bash
$ docker images distroless-musl
REPOSITORY        TAG       IMAGE ID       CREATED              SIZE
distroless-musl   latest    253ca858723c   About a minute ago   872kB
```

## Signing

All distroless images are signed using [Sigstore](https://www.sigstore.dev/). This can be verified
using the [cosign](https://github.com/SigStore/cosign) tool:

```
$ COSIGN_EXPERIMENTAL=1 cosign verify distroless.dev/musl-dynamic | jq
<details>
Verification for distroless.dev/musl-dynamic:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - Any certificates were verified against the Fulcio roots.
[
  {
    "critical": {
      "identity": {
        "docker-reference": "ghcr.io/distroless/musl-dynamic"
      },
      "image": {
        "docker-manifest-digest": "sha256:1478af9ac27cab7f15806cc8534f72e2f0a384282f3de1be9e8c51821834a8db"
      },
      "type": "cosign container image signature"
    },
    "optional": {
      "1.3.6.1.4.1.57264.1.2": "schedule",
      "1.3.6.1.4.1.57264.1.3": "7c2d976e400e325ec06fe714a269d206864b30de",
      "1.3.6.1.4.1.57264.1.4": "Create Release",
      "1.3.6.1.4.1.57264.1.5": "distroless/musl-dynamic",
      "1.3.6.1.4.1.57264.1.6": "refs/heads/main",
      "Bundle": {
        "SignedEntryTimestamp": "MEQCIE/BEaFYgoV5nOchN94zpDdWTdn3JvYxuO8P1nHyEPogAiAOgCZNKWSm82OSxsdJr9SREXUUwekf5+tCwhGsvWnBzw==",
        "Payload": {
          "body": "eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiIyOGY4NzcxODJiODVjZGFhYzM3N2E2MWJiNmZkYjM4ZTFkMTc0OTE2MmU2NTU3Y2I5MTU0NjVmMzQwZDc5MjRlIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FWUNJUUNZVDZyeGh0dTFYZk5NcXRudS9JZ2JHWXllLzhrQ0k4VkM0U3VQb3FheVNRSWhBSmNaY0NYUkhtUlFUYkZLcE02SDY0SklQNTc5eWUwTXNiU1lHZmg3a3NVTCIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCRFJWSlVTVVpKUTBGVVJTMHRMUzB0Q2sxSlNVUnhha05EUVhwRFowRjNTVUpCWjBsVlZqQnZjeXRaTVZoYWVVZzBVbUU0UkZGcWNFWmxlRE0yV2xkVmQwTm5XVWxMYjFwSmVtb3dSVUYzVFhjS1RucEZWazFDVFVkQk1WVkZRMmhOVFdNeWJHNWpNMUoyWTIxVmRWcEhWakpOVWpSM1NFRlpSRlpSVVVSRmVGWjZZVmRrZW1SSE9YbGFVekZ3WW01U2JBcGpiVEZzV2tkc2FHUkhWWGRJYUdOT1RXcEpkMDlFUVhsTlJFbDVUV3BCTUZkb1kwNU5ha2wzVDBSQmVVMUVTWHBOYWtFd1YycEJRVTFHYTNkRmQxbElDa3R2V2tsNmFqQkRRVkZaU1V0dldrbDZhakJFUVZGalJGRm5RVVZ1UWxScGJrRXdRbFJoVkhCUFRGbGxlbTU0WjBkWE1GcHhVRFpDSzJkcVQybEdjemdLYzBwVldEWmxhSFJOVkdKcGJHMHJkVWd4VDFvMFVYbzRibkJCTjFCWE1scDRSa2xSTkRad2RrVnBXbEJJWlhseWJVdFBRMEZyT0hkblowcE1UVUUwUndwQk1WVmtSSGRGUWk5M1VVVkJkMGxJWjBSQlZFSm5UbFpJVTFWRlJFUkJTMEpuWjNKQ1owVkdRbEZqUkVGNlFXUkNaMDVXU0ZFMFJVWm5VVlU1YW1nNENqWnZXakpoTkZWTlJrUnJla2RHVldFeFltaDBPVFJKZDBoM1dVUldVakJxUWtKbmQwWnZRVlV6T1ZCd2VqRlphMFZhWWpWeFRtcHdTMFpYYVhocE5Ga0tXa1E0ZDFwM1dVUldVakJTUVZGSUwwSkdNSGRYTkZwYVlVaFNNR05JVFRaTWVUbHVZVmhTYjJSWFNYVlpNamwwVERKU2NHTXpVbmxpTW5oc1l6Tk5kZ3BpV0ZaNllrTXhhMlZYTldoaVYyeHFUSGsxYm1GWVVtOWtWMGwyWkRJNWVXRXlXbk5pTTJSNlRETktiR0pIVm1oak1sVjFaVmRHZEdKRlFubGFWMXA2Q2t3eWFHeFpWMUo2VERJeGFHRlhOSGRQVVZsTFMzZFpRa0pCUjBSMmVrRkNRVkZSY21GSVVqQmpTRTAyVEhrNU1HSXlkR3hpYVRWb1dUTlNjR0l5TlhvS1RHMWtjR1JIYURGWmJsWjZXbGhLYW1JeU5UQmFWelV3VEcxT2RtSlVRVmRDWjI5eVFtZEZSVUZaVHk5TlFVVkRRa0ZvZWxreWFHeGFTRlp6V2xSQk1ncENaMjl5UW1kRlJVRlpUeTlOUVVWRVFrTm5NMWw2U210UFZHTXlXbFJSZDAxSFZYcE5hbFpzV1hwQk1scHRWVE5OVkZKb1RXcFpOVnBFU1hkT2FtY3lDazVIU1hwTlIxSnNUVUozUjBOcGMwZEJVVkZDWnpjNGQwRlJVVVZFYTA1NVdsZEdNRnBUUWxOYVYzaHNXVmhPYkUxRFZVZERhWE5IUVZGUlFtYzNPSGNLUVZGVlJVWXlVbkJqTTFKNVlqSjRiR016VFhaaVdGWjZZa014YTJWWE5XaGlWMnhxVFVJd1IwTnBjMGRCVVZGQ1p6YzRkMEZSV1VWRU0wcHNXbTVOZGdwaFIxWm9Xa2hOZG1KWFJuQmlha05DYVhkWlMwdDNXVUpDUVVoWFpWRkpSVUZuVWpsQ1NITkJaVkZDTTBGQmFHZHJka0Z2VlhZNWIxSmtTRkpoZVdWRkNtNUZWbTVIUzNkWFVHTk5OREJ0TTIxMlEwbEhUbTA1ZVVGQlFVSm5iSGhqVDJSalFVRkJVVVJCUldkM1VtZEphRUZNV1ZBNGFHeFBaRkp5U1N0aFNrWUthREZDVG1WNFNHTk1ZVEF3ZVhwc2VrRjJjR1JNYUROR1RqSlZhMEZwUlVGMFpWTmhka2syVEV4RVpHVTBaVTVpT0ZKUmEzZG1SMjlWZDFWSlJVdFdad3BIWXpoWFJFSlFaSE5EWjNkRFoxbEpTMjlhU1hwcU1FVkJkMDFFWVVGQmQxcFJTWGRrUVdoa05rdG5iMUp3YVhadU9VZDFNbVpSUjIxWFRreFRRaTgxQ2tOQ1dWRkdTak0yVjNCTmJUSnpNR0ZwTDJ0blYxTlNOamM0Wkd0Qk9FaGFNMlZ3Y1VGcVJVRnZjamcyUVU1T1REQjRVRk51VlZneVZXUlZTbGR0VXprS2MycG5XV1ExYWxaU1YyRnNTV1ZHWmtsWWVrRlNZWFJzVWtKT1RsbGpZa3h1WlhJMWRrODRRd290TFMwdExVVk9SQ0JEUlZKVVNVWkpRMEZVUlMwdExTMHRDZz09In19fX0=",
          "integratedTime": 1659406945,
          "logIndex": 3085662,
          "logID": "c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"
        }
      },
      "Issuer": "https://token.actions.githubusercontent.com",
      "Subject": "https://github.com/distroless/musl-dynamic/.github/workflows/release.yaml@refs/heads/main",
      "run_attempt": "1",
      "run_id": "2779179436",
      "sha": "7c2d976e400e325ec06fe714a269d206864b30de"
    }
  }
]
</details>
