# No variable expansion

[https://stackoverflow.com/a/58486645/11217661](https://stackoverflow.com/a/58486645/11217661)

In case you want to echo %MY_VAR% without expansion, escaping as %%MY_VAR%% does not work. Only escaping as %MY_VAR^% or ^%MY_VAR^% will output %MY_VAR% as intended.

```bash
set FOO=BAR
echo %FOO%
BAR
echo %%FOO%%
%BAR%
echo %FOO^%
%FOO%
echo ^%FOO^%
%FOO%
echo "%FOO%"
"BAR"
echo "%%FOO%%"
"%BAR%"
echo "%FOO^%"
"%FOO^%"
echo "^%FOO^%"
"^%FOO^%"
echo ^"%FOO%"
"BAR"
echo ^"%FOO%^"
"BAR"
echo "%FOO%^"
"BAR^"
echo ^"%FOO^%"
"%FOO%"
echo ^"^%FOO^%"
"%FOO%"
echo ^"^%FOO^%^"
"%FOO%"
```