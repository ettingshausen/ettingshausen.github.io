---
layout: post
title:  "Windows10 Ubuntu nextcloud 存储目录权限"
date:   2017-03-22 09:09:06 +0800
categories: nextcloud
---

为了方便文件管理，修改了data的默认路径到其他盘，但是打开网页提示：
>Data directory (%s) is readable by other users,Please change the permissions to 0770 so that the directory cannot be listed by other users.

可以通过修改源码，禁用权限检查。
>vim /var/www/nextcloud/lib/private/legacy/util.php

找到方法`checkDataDirectoryPermissions`
修改为：
```php
public static function checkDataDirectoryPermissions($dataDirectory) {
		$l = \OC::$server->getL10N('lib');
		$errors = array();
		$permissionsModHint = $l->t('Please change the permissions to 0770 so that the directory'
			. ' cannot be listed by other users.');
		$perms = substr(decoct(@fileperms($dataDirectory)), -3);
		if (substr($perms, -1) != '0') {
			chmod($dataDirectory, 0770);
			clearstatcache();
			$perms = substr(decoct(@fileperms($dataDirectory)), -3);
			if (substr($perms, 2, 1) != '0') {
				$errors[] = array(
					'error' => $l->t('Data directory (%s) is readable by other users', array($dataDirectory)),
					'hint' => $permissionsModHint
				);
			}
		}
		return $errors;
	}
```