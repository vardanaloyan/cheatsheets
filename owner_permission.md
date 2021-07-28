# Unix permissions cheatsheet
```
-rw-r--r--   1 Vardan_Aloyan  staff     6143 Jul  9 13:41 data.py
```

```
drwxr-xr-x   3 Vardan_Aloyan  staff       96 Jul 15 00:37 VirtualBox VMs
```

`
(*)(***)(***)(***)
`

* `(*)` can be `-` meaning `file` or `d` meaning directory
* `(***)` User permission, can be `r` read, `w` write, `x` execute or `-` no permission
* `(***)` Group permission, can be `r` read, `w` write, `x` execute or `-` no permission
* `(***)` Other/World permission, can be `r` read, `w` write, `x` execute or `-` no permission

--

This sets permissions for specific users, without changing the ownership of the directory.

```
setfacl -m u:username:rwx myfolder
```


* setfacl = set File ACL, ACL = Access Control List hence for short "setFacl"

* If you want to apply it recursively to all the subdirectories: add the -R flag like this: 
	
		setfacl -R -m u:username:rwx myfolder

--
References

* [https://www.guru99.com/file-permissions.html](https://www.guru99.com/file-permissions.html)
