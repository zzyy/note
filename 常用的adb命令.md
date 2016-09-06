- adb shell am broadcast 后面的参数有：

[-a

<action>]
[-d <data_uri>]
[-t <mime_type>]
[-c <category> [-c <category>] ...]
[-e|--es <extra_key>
  <extra_string_value> ...]
[--ez <extra_key>
  <extra_boolean_value> ...]
[-e|--ei <extra_key>
  <extra_int_value> ...]
[-n <component>]
[-f <flags>] [<uri>]</uri></flags></component></extra_int_value>
</extra_key></extra_boolean_value>
</extra_key></extra_string_value>
</extra_key></category></category></mime_type></data_uri></action>

例如： adb shell am broadcast -a com.android.test --es test_string "this is test string" --ei test_int 100 --ez test_boolean true
