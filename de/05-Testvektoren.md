# Testvektoren

Zur Veranschaulichung des Konzepts und Überprüfung der Implementierung werden hier Szenarien mit Testvektoren beschrieben.

Im Folgenden hat Alice das Nutzerpasswort `password123456` sowie ein Kyber- und ein RSA-Schlüsselpaar:


* Kyber

<details>
  <summary>Kyber Public Key</summary>

```
-----BEGIN KYBER PUBLIC KEY-----
MIIFQzCBlwYJKoZIhvcNAQMBMIGJAkEA/KaCzo4Syrom78z3EQ5SbbB4sF7ey80e
tKII864WF64B81uRpH5t9jQTxeEu0ImbzRMqzVDZkVG9xD7nN1kuFwJAZ4Rxsnqc
9E7pGknFFH2xqaryRPBaQ01khpMdLRQnG541Awtx/XPaF5Bpsy4pNWMOHCBiNU0N
ogpsQW5QvnlMpAICBKADggSlAAKCBKD140MJwcgwcKrKV28DeXvN14TBVA4/gFQ4
VXoswE/UvL1UlZudxJg1w8n/OsimR4nlwoVdOSwAZSGOemvjyH/cfGQvC64nhwm0
WJojIRzVaF9qwrSBEbjLi8wSE4wblAK41baYlnCrd3TOJWz0mMdGeqKO+Tgv0hiL
533gl07cea/agXMjk84HO8FLGzRDGVcrAHm9ZnSJkII3cx3/NXfNpYX8aE+pcoHI
sCAVmme+2iOVk3grQR9CZaVg3EGV9qnEaxNKlHDlFRsSm2JpMS2OFCnMm8oC6VVZ
VjVJm7v9iinxO7fZlDzLg2LWwzdZpsyrVXI99GENI2f7NLdNR874tE9OBGC5Oytx
uUHvAkkChnuOhTi24z+ZWsMqUKJnBZs6J19gKpXtEhcGJT5SYZAGqAQZ5QreqnrT
hj1h6LXZIm5jMJJGLIuzHFLiAnYr2JpeQyxgYEfMTGkgF1sxImLBHFAfqpgAvInF
97cwU0TO+BD22kZwAK4rnLnxR3XqQLOmUEETQj67F02PaDJbLBMUGxp44a0ziCGY
8bRStc0h2oZhR3s2MSSA/K3SxHda6AFV4sRMuweLMcmYc4Xl+xHBNIPY9iuEOzMk
h4044TkDSScj+CMXlLx93M1dLFAWAq56uhYHbJ5s95/lFzlqZQbgFYSgzA1Dubws
i5DkfLZc9mYW2YUHLByL0hjAhRFUKL7tyjrl9KWMk03TOZToQgLVfC1UlFd4gAo+
AogPucNXaJnw0ZKiZqqbMXfMmqY4iGhq6xXXCE45cQMutn9G1huHW2TRWqOiV4yM
sW2zaWQRo0xRWyeGLFUuMh8gOkWuZ0Q1ibiitscMFWzEe29K42HVNDUc6GWSa1Lh
9XdkRD+wk8Vj0X3OWnKuMZ71p5F1GZVJaFtGIYyXuBaRES9hiilMdpZOapjZIERc
VkJCJxaegGplWLA+bG2GzJhd9KPShTv6kgw+sVtlU1SF60WHVRydSzWQ25ZWdZRr
AM1LdcwIMnvrYyTPsLJc2kpXelMzdcDSNDaDRKc1xxSDU4X8s8octhB4sS8f5YdV
zLjO844L3BS0WZP+CKvpWSBvkKVJMoZ/5Itp1cZIdH2y95JMejJsJp9h2h6BARaH
S34c2hikQb3X16AayHcEKonwvL1VWYJFiQsX8jlZcVfK4311h14e2w7P7Lshe6mH
9DwgY17VZ0HDEDzFZUAYhyzAwKulFo5GgT/KFlP1Q2pnJAXN54EAFZebo77EqH4n
/I7csx35HD1jxzWwh6IFfKqLy31b2UKIlqX7aJEw4DO7uiufq7RwwFYgc8+3ZS+y
oRzPSR0PJL6Q4DGWe5tqt79y8gY1DKEuAj5ELAzuFXqJzC1TyoYq0ZEDWinepCIN
lk6ljFEe2HZ8k8DqAJgMSgB3RT/+csCXSQbrzLDPBjqrVDO66i90SHvTCKuYZs0G
sYSrh6vGZJwRPInJsc5nrIDL5b4VQbU1ghWkpUgkJUJ/kou/e8vJdDOBd39Z14S/
bG+sYzMvVa/1Ihstgn1aJWTv673LmFcVkeYSKLyZ2RKbk2sIfScTo9qo0T3qH3m/
JXdCOWlbzA==
-----END KYBER PUBLIC KEY-----
```
</details>

<details>
  <summary>Kyber Private Key</summary>

```
-----BEGIN KYBER PRIVATE KEY-----
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
WsAJgVzIB4Ihrfj/AfAvb9XbYOdEcBOmQQ==
-----END KYBER PRIVATE KEY-----
```
</details>

* RSA
<details>
  <summary>RSA Public Key</summary>

```
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAuc02QTELwzsPWfXKyEgr
xqQgm170JR3l0Au8QioIz22/hNykyFQtpV7/Je+CjBfA5ApVwEE+am7YYpYFvO/o
xP/PT/zwkHCz9nSSYrZTna8wwLu9bggWuxSU2taysR7f4RlRgctBwwDs2553h9Xd
owAVOqVzfDhr6j8qsAhS8xV0/WnIHW87L52VUHfkNLg2RLIsqplcB2TChgCOHNZB
+WUtA92mmV0s3o/7AJjeZmiCnirYdrFP9n0NtHE0fycngVKQhgJzf3LrT7PDO/wu
Q2h2W7wXfLCOS/TSNx/MbGyU+BGk3QDf3PeMYhMfgUIwBmo1OosXnz+u2AqqCW5k
UEJ2UiO8Mk4o8BtU37twf4X0idUO2Hq9KmuXbZ3UWDBkhKfuP8ZWOfI3fdoHgBMb
QHM3w4qu1GNFy22wCtMGaiDIlnHB4XF+0pCcEvj7b1cXylcA0Qr0dkGkPENVP50N
9+8kuCKZrpU8135ymH+rO/En5+BpAeakXy8YXNBHli2biS9b7yqzz/N3W84ncHuz
6s+lcbGQzWoBZylQvYbl+YB65K3uuTDMPFbX/pQ0lwltwwjbpu8vmE4UKN+o7614
DhyqRanCk1jkDxoGppr/v4AK0/6IfOQiQdkWdFc6IGvG41bBquV4lBX5Br0Zgpqf
FopIXsQUej/snkw1oPqhsJ0CAwEAAQ==
-----END PUBLIC KEY-----
```
</details>

<details>
  <summary>RSA Private Key</summary>

```
-----BEGIN PRIVATE KEY-----
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
HTwMtMu9FJxg9m7T+NioXb6vHMrJ+KeO
-----END PRIVATE KEY-----
```
</details>


## Alice erstellt Schlüsselmaterial für ein Board

1. Alice generiert einen Board Key (32 zufällige Bytes):
<details>
  <summary>Source Code</summary>

```python
board_key = random_bytes(32)

print('board_key:', base64.b64encode(board_key))
```
</details>

```python
board_key: pKua3nhSlRKnTjtOwbnz3D/wmqagNe6A1TC2NhGhOCs=
```

2. Alice erstellt und verschlüsselt ein erstes Geheimnis mit Kyber:
<details>
  <summary>Source Code</summary>

```python
kyber_kem_result     = Kyber.encapsulate(kyber_private_key, kyber_public_key)
secret1              = kyber_kem_result.secret
encapsulated_secret1 = kyber_kem_result.encapsulated_secret

print('secret1:             ', secret1)
print('encapsulated_secret1:', encapsulated_secret1)
```
</details>

```python
secret1:              66dWmchrfIuHMqMDMzw1CpPsBj2SU+zlwfQRv5HSJus=
encapsulated_secret1: MIIE4zCBlwYJKoZIhvcNAQMBMIGJAkEA/KaCzo4Syrom78z3EQ5SbbB4sF7ey80etKII864WF64B81uRpH5t9jQTxeEu0Imbz
                      RMqzVDZkVG9xD7nN1kuFwJAZ4Rxsnqc9E7pGknFFH2xqaryRPBaQ01khpMdLRQnG541Awtx/XPaF5Bpsy4pNWMOHCBiNU0Nog
                      psQW5QvnlMpAICBEADggRFAAKCBEDxohEP3AcCbSzZWNhCywBx29wFklldizylVeSxm1PxTfr8+RcJ0R1n38IEIT/smVugHbE
                      NQOfbZ2E8i+re43GSSoEWs1N1Z9HNFh+UcmQedpsd8sDNjHObezQ3JUzFYcbkOFiZCrFyrgJ73+OkpiIRHAbH64+6Y83JYVB6
                      ajfuP14OHYQcZjGJUklXwUaDTEeENVK8K26GPRdJ6E/17MOc3Sgq+CvgrpNRMxquuA0h8GgUXCe+K3APuvD37k2+tUQfIugr/
                      JnkiwS0k0JLSyurqffEWX37zeKGpjvNZubcRzCPa59x+SHeINGGHkV6I0tquFIr5JznXLXguBbWJRdSM1u9Wv66q9YsGV25KD
                      TyjCYmYYRFMagl0qg7+R8sQXNmj0MmfZ8oKGrOpkT8xPcAT2qHy7CFqfQkrN6AsOu2DSb7TOHrzy03LXzvgPg1ROBLZK4PbcG
                      rvDOaq+pHdRxzpxQjxyUKZe0jy2L5J2ImEEJZ8zIV2msqqK/YwOyMDVGpF7GwLrs7oKb1uOrZ+y9eLKV8NLdHkfSgheujFqc/
                      g67WvGjQGOZx0NPgDt+qe/2k13Qlpql5K4D8a/0uWrWBUCt1iUGdoYiHLxkz7EY2c/C05tmXYgSCV9/4YxRGXwfHesRRDqRwx
                      opcFR0trb4PNxE/bCfq52MWcz1U2DFVEcHkZYKBpuVrwcuDwWNpJyq5/38yo/8kcBbTnO0rQBHAoXthS4b3eJr54OIix4IUgN
                      xtiY1wRmbLZn3uDyvHaWVceitIcQFhh9GZRiMuoqQgCFpfaHPp41ULS+RFh13Si6luwEaxTyCskfkA56qjNmC569X2vsr4n0T
                      MY9kQNPSMDfbX0t5bJztyL9Ts/DzoKFwb/2M1a2qBxFvIYDICuvT1z+kR9r5MEWIxbZW4U4UbFeDMSWm14kmBZIYuJ+1TTi0x
                      3ev8+vwfFscvsx+A0WAbvRX5+jxPCVvoUIwRdbV47ORjxkeGeWmLjMaxVz3yPtYUvmJUH+HrNCDahtD07mYabyxtRa5un1L38
                      nM4Jp0N0+jQ4CWFIdk28tADqcMo/p7lfkm2EC8wflWeJwC0sdsoEdR9o7D7aWdJL1kT2j2z6T4oqh9ddKTJLIXgvKQgSCMGic
                      loKYHQbsOoJoBFQ/KhXKx8pZbYJOc/qf3iK6MfCkQuvYyca2z5Bk0P3HcfpGOAEwhdA0Yn/6rIhMR9Wnmx8FjPO64gD234ghu
                      MU5PzftIUMdWMeSpdbkjwP4W+5JuZrhzTFdUpOzJVkVVjhQrQQF3Da8G2tpw28gimn7xm8I+wPkPFVn3/0Rnsr3rgA64Z6/pS
                      lcXAXSkMVDN6+Ip3kE4InLQ0cN99HJmbUrO6Rjnf0pBDzWsLW9wgNtgewpAz7tmiRxftWmwpqeh0c88v+rRF+IbjALTUEHUyP
                      wRsTYc5fOxUYN6z2J3FmqImWg==
```

3. Alice erstellt und verschlüsselt ein zweites Geheimnis mit RSA:
<details>
  <summary>Source Code</summary>

```python
rsa_kem_result       = RSA.encapsulate(rsa_private_key, rsa_public_key)
secret2              = rsa_kem_result.secret
encapsulated_secret2 = rsa_kem_result.encapsulated_secret

print('secret2:             ', secret2)
print('encapsulated_secret2:', encapsulated_secret2)
```
</details>

```python
secret2:              R5do1jbeqyptBbGafLz3eMs6QpdLQYnMhxEUhswqtvft1DAKFn0mYk0nXvO7/ltUxM9IjXGb0aJrU89I26dZfYOQ8drZiFgsp
                      JMwpHjy6GMDMhLb2Yox4i3scrkCZS+ap8Zk2sTk1/VdERxZdbCKaVI+K0LAeI43lFkYeoMpir9MkNArSI/f2zNWW5BCqmXZcZ
                      JUKcQWfvwGv1ovg0qs3ifN6bX10H8MvgELBudEq3DqGBgiH7arFZeT2/m1Fubq48tMS4tKLs1HzSOQP4OVhjkav6fqsKMFdKd
                      +5wecKcgGDCvwBkLCKWR8T2Ie0sNs7XJ4xeTUFGcPwZFhmJ7og8Xb+HaiQ+DZWxUuBjqs7h+Pp85V/T6SIMkZpFBw1DCSQlIh
                      fZDCaAK0wM2zDLWHWveM+xoe9KLQaxr2GHAD6N02uJ2UvwY+ZSlUvoulYC6sGQLtAmN7M1D5x0GqPIBr/03btjD4ECa7vz5+q
                      g4CgMbS545oLVI+oYpw43KoGR1Qhjt0VfmBoJuf2YrI4hr85LdBUCmewcDmSz78Es+YLcIH7+YspXNAXwV2pmip2hf7YuKbXI
                      q4nx9TC5BzgO1IgSMO6agigZt97xzcvJ8Nw1SbD45SSdCUnnE7V1ZywR/H7EKGKKi+60fvwGB7IK0Q9oTrXkyni33MQNweO27
                      KYqs=
encapsulated_secret2: Bu4B4ptBfjKLWWtMH1+WgENwclZNlz0uJs3TKgyxZop2HFo+wz+NBaNijwKixlLuF6H5YR22raAjjQR8qveGfRKHvsv8ldsEU
                      g5cNdbJ413GZrAwGio5LID4z3sCRbKpAThQg1ho+qqiLpX08jfup0Rk2ib7MVJCi8Ax7U6ZjZtM3dBr0IIWWlSWarzyyP9e83
                      PGguaqsTqUTc/lKenbcenuNNzhH/o3Aaw3/ywv7hsJQgWbrO/CwjIYJVNtk9s6Y0AfVLP6z9BMruSsO+zRRb93HOcFNvdYbga
                      iYw/mqtbC1mSuzFsFJKrFwPzu8J+AGP28Axdxpe/wlDvYPio+mnoP3YmaEOE34BeEVRIHDDUHg5s92lajTqEs7+19ykLIdPMg
                      A3me+p305bxWXX605UoXLr3C0YMGvdhcE0S4/cOjp0cLMM18uHxfln5eeKcNkiqZB4EltfDTsF6z/VTLYcSfyP0hkSYR1OJ9z
                      FdjbBXwRQs3yDxLb/4msQtH6xeTmqwiO2jd0Wzh6hjilUU0/aDzpMa0ZE+mhtLCDUtvQa/qUYeFStEPfdm6eulNRSCWVbutr3
                      +a3iG5bcI5DCQ21IeHRJtAqLRZhcY/zYpNvlEQDQ7IZ0x9aUTEmmLJ71kNO+2Yhkzfmrl9dDK8Nx0xcSqsZO6T4xSJormtfMZ
                      njCc=
```

4. Alice verschlüsselt den Board Key
<details>
  <summary>Source Code</summary>

```python
key_encryption_key  = hkdf(secret1 || secret2)
encrypted_board_key = aes256kw.encrypt(board_key, key_encryption_key)

print('key_encryption_key: ', key_encryption_key)
print('encrypted_board_key:', encrypted_board_key)
```
</details>

```python
key_encryption_key:  pTvOc7Sj9Hqi9KyVvzdjqngU+sQJZOSW4/0qCJJYaOY=
encrypted_board_key: 5+v3LhNeFW7yGsJzaSJKAhRBnFkw2bB8nKKnK9rr3gaa3jMqfghb9Q==
```

## Alice öffnet ein Board, das sie zuvor erstellt hat

1. Alice hat das verschlüsselte Schlüsselmaterial bereits vorliegen

```python
encrypted_board_key:     5+v3LhNeFW7yGsJzaSJKAhRBnFkw2bB8nKKnK9rr3gaa3jMqfghb9Q==
encapsulated_kdf_input1: MIIE4zCBlwYJKoZIhvcNAQMBMIGJAkEA/KaCzo4Syrom78z3EQ5SbbB4sF7ey80etKII864WF64B81uRpH5t9jQTxeEu0I
                         mbzRMqzVDZkVG9xD7nN1kuFwJAZ4Rxsnqc9E7pGknFFH2xqaryRPBaQ01khpMdLRQnG541Awtx/XPaF5Bpsy4pNWMOHCBi
                         NU0NogpsQW5QvnlMpAICBEADggRFAAKCBEDxohEP3AcCbSzZWNhCywBx29wFklldizylVeSxm1PxTfr8+RcJ0R1n38IEIT
                         /smVugHbENQOfbZ2E8i+re43GSSoEWs1N1Z9HNFh+UcmQedpsd8sDNjHObezQ3JUzFYcbkOFiZCrFyrgJ73+OkpiIRHAbH
                         64+6Y83JYVB6ajfuP14OHYQcZjGJUklXwUaDTEeENVK8K26GPRdJ6E/17MOc3Sgq+CvgrpNRMxquuA0h8GgUXCe+K3APuv
                         D37k2+tUQfIugr/JnkiwS0k0JLSyurqffEWX37zeKGpjvNZubcRzCPa59x+SHeINGGHkV6I0tquFIr5JznXLXguBbWJRdS
                         M1u9Wv66q9YsGV25KDTyjCYmYYRFMagl0qg7+R8sQXNmj0MmfZ8oKGrOpkT8xPcAT2qHy7CFqfQkrN6AsOu2DSb7TOHrzy
                         03LXzvgPg1ROBLZK4PbcGrvDOaq+pHdRxzpxQjxyUKZe0jy2L5J2ImEEJZ8zIV2msqqK/YwOyMDVGpF7GwLrs7oKb1uOrZ
                         +y9eLKV8NLdHkfSgheujFqc/g67WvGjQGOZx0NPgDt+qe/2k13Qlpql5K4D8a/0uWrWBUCt1iUGdoYiHLxkz7EY2c/C05t
                         mXYgSCV9/4YxRGXwfHesRRDqRwxopcFR0trb4PNxE/bCfq52MWcz1U2DFVEcHkZYKBpuVrwcuDwWNpJyq5/38yo/8kcBbT
                         nO0rQBHAoXthS4b3eJr54OIix4IUgNxtiY1wRmbLZn3uDyvHaWVceitIcQFhh9GZRiMuoqQgCFpfaHPp41ULS+RFh13Si6
                         luwEaxTyCskfkA56qjNmC569X2vsr4n0TMY9kQNPSMDfbX0t5bJztyL9Ts/DzoKFwb/2M1a2qBxFvIYDICuvT1z+kR9r5M
                         EWIxbZW4U4UbFeDMSWm14kmBZIYuJ+1TTi0x3ev8+vwfFscvsx+A0WAbvRX5+jxPCVvoUIwRdbV47ORjxkeGeWmLjMaxVz
                         3yPtYUvmJUH+HrNCDahtD07mYabyxtRa5un1L38nM4Jp0N0+jQ4CWFIdk28tADqcMo/p7lfkm2EC8wflWeJwC0sdsoEdR9
                         o7D7aWdJL1kT2j2z6T4oqh9ddKTJLIXgvKQgSCMGicloKYHQbsOoJoBFQ/KhXKx8pZbYJOc/qf3iK6MfCkQuvYyca2z5Bk
                         0P3HcfpGOAEwhdA0Yn/6rIhMR9Wnmx8FjPO64gD234ghuMU5PzftIUMdWMeSpdbkjwP4W+5JuZrhzTFdUpOzJVkVVjhQrQ
                         QF3Da8G2tpw28gimn7xm8I+wPkPFVn3/0Rnsr3rgA64Z6/pSlcXAXSkMVDN6+Ip3kE4InLQ0cN99HJmbUrO6Rjnf0pBDzW
                         sLW9wgNtgewpAz7tmiRxftWmwpqeh0c88v+rRF+IbjALTUEHUyPwRsTYc5fOxUYN6z2J3FmqImWg==
encapsulated_kdf_input2: Bu4B4ptBfjKLWWtMH1+WgENwclZNlz0uJs3TKgyxZop2HFo+wz+NBaNijwKixlLuF6H5YR22raAjjQR8qveGfRKHvsv8ld
                         sEUg5cNdbJ413GZrAwGio5LID4z3sCRbKpAThQg1ho+qqiLpX08jfup0Rk2ib7MVJCi8Ax7U6ZjZtM3dBr0IIWWlSWarzy
                         yP9e83PGguaqsTqUTc/lKenbcenuNNzhH/o3Aaw3/ywv7hsJQgWbrO/CwjIYJVNtk9s6Y0AfVLP6z9BMruSsO+zRRb93HO
                         cFNvdYbgaiYw/mqtbC1mSuzFsFJKrFwPzu8J+AGP28Axdxpe/wlDvYPio+mnoP3YmaEOE34BeEVRIHDDUHg5s92lajTqEs
                         7+19ykLIdPMgA3me+p305bxWXX605UoXLr3C0YMGvdhcE0S4/cOjp0cLMM18uHxfln5eeKcNkiqZB4EltfDTsF6z/VTLYc
                         SfyP0hkSYR1OJ9zFdjbBXwRQs3yDxLb/4msQtH6xeTmqwiO2jd0Wzh6hjilUU0/aDzpMa0ZE+mhtLCDUtvQa/qUYeFStEP
                         fdm6eulNRSCWVbutr3+a3iG5bcI5DCQ21IeHRJtAqLRZhcY/zYpNvlEQDQ7IZ0x9aUTEmmLJ71kNO+2Yhkzfmrl9dDK8Nx
                         0xcSqsZO6T4xSJormtfMZnjCc=
```

2. Alice entschlüsselt das erste Geheimnis mit Kyber

<details>
  <summary>Source Code</summary>

```python
secret1 = Kyber.decapsulate(encapsulated_kdf_input1, kyber_private_key, kyber_public_key)

print('secret1:', secret1)
```
</details>

```python
secret1: 66dWmchrfIuHMqMDMzw1CpPsBj2SU+zlwfQRv5HSJus=
```
3. Alice entschlüsselt das zweite Geheimnis mit RSA

<details>
  <summary>Source Code</summary>

```python
secret2 = RSA.decapsulate(encapsulated_kdf_input2, rsa_private_key, rsa_public_key)

print('secret2:', secret2)
```
</details>

```python
secret2: R5do1jbeqyptBbGafLz3eMs6QpdLQYnMhxEUhswqtvft1DAKFn0mYk0nXvO7/ltUxM9IjXGb0aJrU89I26dZfYOQ8drZiFgspJMwpHjy6GMDMh
         Lb2Yox4i3scrkCZS+ap8Zk2sTk1/VdERxZdbCKaVI+K0LAeI43lFkYeoMpir9MkNArSI/f2zNWW5BCqmXZcZJUKcQWfvwGv1ovg0qs3ifN6bX1
         0H8MvgELBudEq3DqGBgiH7arFZeT2/m1Fubq48tMS4tKLs1HzSOQP4OVhjkav6fqsKMFdKd+5wecKcgGDCvwBkLCKWR8T2Ie0sNs7XJ4xeTUFG
         cPwZFhmJ7og8Xb+HaiQ+DZWxUuBjqs7h+Pp85V/T6SIMkZpFBw1DCSQlIhfZDCaAK0wM2zDLWHWveM+xoe9KLQaxr2GHAD6N02uJ2UvwY+ZSlU
         voulYC6sGQLtAmN7M1D5x0GqPIBr/03btjD4ECa7vz5+qg4CgMbS545oLVI+oYpw43KoGR1Qhjt0VfmBoJuf2YrI4hr85LdBUCmewcDmSz78Es
         +YLcIH7+YspXNAXwV2pmip2hf7YuKbXIq4nx9TC5BzgO1IgSMO6agigZt97xzcvJ8Nw1SbD45SSdCUnnE7V1ZywR/H7EKGKKi+60fvwGB7IK0Q
         9oTrXkyni33MQNweO27KYqs=
```
4. Alice entschlüsselt den Board Key:

<details>
  <summary>Source Code</summary>

```python
encryption_key = hkdf(secret1 || secret2)
board_key      = aes256kw.decrypt(encrypted_board_key, encryption_key)

print('encryption_key:', encryption_key)
print('board_key:     ', board_key)
```
</details>

```python
encryption_key: pTvOc7Sj9Hqi9KyVvzdjqngU+sQJZOSW4/0qCJJYaOY=
board_key:      pKua3nhSlRKnTjtOwbnz3D/wmqagNe6A1TC2NhGhOCs=
```
