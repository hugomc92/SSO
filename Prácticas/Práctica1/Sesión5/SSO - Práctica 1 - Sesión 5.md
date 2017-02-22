# Práctica 1 - Administración de la seguridad en Linux


## Sesión 5 - SELinux (Security Enhanced Linux)

### **Hugo Maldonado Cózar**
# <a></a>

### **Ejercicio 5.1**
#### Crear un usuario `SELinux` denominado admin con el rol `sysadm_r`

Para crear un usuario `SELinux` vamos primero a crear un usuario del sistema y luego vamos a asociarlo a un usuario `SELinux` que creemos. Para ello:

```
$ sudo -i

# useradd admin_s

# semanage user --roles 'sysadm_r' --prefix admin --add admin

# semanage login --add --seuser admin admin_s

# passwd admin_s
Cambiando la contraseña del usuario admin_s.
Nueva contraseña: 
Vuelva a escribir la nueva contraseña: 
passwd: todos los tokens de autenticación se actualizaron exitosamente.

# chcon -R -u admin /home/admin_s/
```

Ya tenemos el usuario `SELinux` creado. Para comprobarlo:

```
# semanage user -l
                Etiquetado MLS/       MLS/                          
Usuario SELinux  Prefijo    Nivel MCS  Rango MCS                      Roles SELinux

admin           admin      s0         s0                             sysadm_r
...
```

# <a></a>
### **Ejercicio 5.2**
#### Localiza algunos mensajes de los logs de tu sistema, o genéralos, y describe la denegación que producen.

Los mensajes de logs en `Fedora`se hacen a través del demonio `auditd`, por lo que el fichero que tenemos que analizar es `/var/log/audit/audit.log`.

Vamos a generar una entrada de log introduciendo la orden `su` pero con una contraseña incorrecta

Ahora vamos a analizar qué entradas del log produce, para ello vamos a usar la orden `ausearch` que nos permite ver el log generado por `auditd` de una forma más fácil:

```
$ sudo ausearch -ts recent
...
----
time->Fri Nov 11 13:17:03 2016
type=USER_AUTH msg=audit(1478866623.789:528): pid=12320 uid=1000 auid=4294967295 ses=4294967295 subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 msg='op=PAM:authentication grantors=? acct="root" exe="/usr/bin/su" hostname=? addr=? terminal=pts/1 res=failed'
```

Podemos ver cómo se ha producido un fallo de autenticación (USER_AUTH)(**[[1]](#ref)**) ejecutando la orden `su` (como se ve en el mensaje: `exe="/usr/bin/su`).

# <a></a>
### **Ejercicio 5.3**
#### Indicar la orden que debemos ejecutar para pasar de un estado permisivo a uno obligatorio.

Como podemos ver en **[[2]](#ref)** para pasar de un estado permisivo a uno obligatorio usamos el comando `setenforce`. Aunque los cambios con este comando se restablecen tras un reinicio, es decir, es un cambio temporal

```bash
# Obtenemos el actual estado de SELinux
$ getenforce 
Enforcing

# Cambiamos el estado a permisivo (porque estaba en obligatorio)
$ sudo setenforce 0

# Comprobamos cómo efectivamente se ha cambiado
$ getenforce 
Permissive

# Para deshacer el cambio
$ sudo setenforce 1

$ getenforce 
Enforcing
```

Para hacer este cambio permanente se podría añadir a los parámetros de configuración del bootloader (`Grub`) el parámetro: `enforcing=0` lo que haría que `SELinux` cambiara a modo permisivo durante el arranque (si lo ponemos =1 forzamos que siempre sea obligatorio).

Otra forma de hacerlo sería cambiar el fichero de configuración de `SELinux`.

# <a></a>
### **Ejercicio 5.4**
#### Completar la tabla anterior para la distribución de Linux que esté usando cada uno de vosotros.

Para obtener los datos de dicha tabla:

```
$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          error (Permission denied)
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      30
```

Más información:

```
$ seinfo 

Statistics for policy file: /sys/fs/selinux/policy
Policy Version & Type: v.30 (binary, mls)

   Classes:            94    Permissions:       259
   Sensitivities:       1    Categories:       1024
   Types:            4794    Attributes:        253
   Users:               9    Roles:              14
   Booleans:          305    Cond. Expr.:       354
   Allow:          102181    Neverallow:          0
   Auditallow:        155    Dontaudit:        8879
   Type_trans:      17954    Type_change:        87
   Type_member:        35    Role allow:         39
   Role_trans:        420    Range_trans:      5753
   Constraints:       109    Validatetrans:       0
   Initial SIDs:       27    Fs_use:             28
   Genfscon:          107    Portcon:           601
   Netifcon:            0    Nodecon:             0
   Permissives:         0    Polcap:              2
```

**[[3]](#ref)**

```
$ seinfo -aunconfined_domain_type -x
   unconfined_domain_type
      abrt_handle_event_t
      anaconda_t
      install_t
      preupgrade_t
      httpd_unconfined_script_t
      authconfig_t
      bacula_unconfined_script_t
      kernel_t
      boinc_project_t
      bootloader_t
      certmonger_unconfined_t
      cinder_api_t
      cinder_backup_t
      cinder_scheduler_t
      cinder_volume_t
      cloud_init_t
      condor_startd_t
      conman_unconfined_script_t
      crond_t
      system_cronjob_t
      unconfined_cronjob_t
      devicekit_t
      devicekit_power_t
      devicekit_disk_t
      dirsrvadmin_unconfined_script_t
      firstboot_t
      fsadm_t
      fwupd_t
      inetd_t
      inetd_child_t
      initrc_t
      kdumpctl_t
      keepalived_unconfined_script_t
      livecd_t
      clvmd_t
      lvm_t
      depmod_t
      insmod_t
      mount_t
      unconfined_mount_t
      unconfined_munin_plugin_t
      nagios_unconfined_plugin_t
      nagios_eventhandler_plugin_t
      openshift_initrc_t
      openvpn_unconfined_script_t
      openwsman_t
      pegasus_openlmi_logicalfile_t
      pegasus_openlmi_unconfined_t
      pki_tomcat_script_t
      prelink_t
      puppetagent_t
      realmd_t
      realmd_consolehelper_t
      cluster_t
      rolekit_t
      rpm_t
      rpm_script_t
      rtas_errd_t
      samba_unconfined_script_t
      samba_unconfined_net_t
      semanage_t
      setfiles_mac_t
      unconfined_sendmail_t
      sge_shepherd_t
      sge_job_t
      sosreport_t
      systemd_coredump_t
      tomcat_t
      tuned_t
      udev_t
      unconfined_service_t
      unconfined_t
      unconfined_dbusd_t
      virtd_t
      virtd_lxc_t
      virt_qemu_ga_unconfined_t
      vmtools_t
      vmtools_helper_t
      vmware_host_t
      watchdog_unconfined_t
      wine_t
      xserver_t
      xdm_unconfined_t
      zabbix_script_t
```

**[[4]](#ref)**

```
$ seinfo -aubac_constrained_type -x
ERROR: Provided attribute (ubac_constrained_type) is not a valid attribute name.
```

Los datos finales de la tabla:

```
Distribution 		Policy 			MLS?		deny_ 			Unconfined 		UBAC?
					store name 					unknown 		domains
----------------------------------------------------------------------------------------------------------------------
Fedora 24 			targeted 		enabled 	allowed 		Sí				No
```

# <a></a>
## Bibliografía <a id="ref"></a>

[1] [Tipos de registro auditd](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Security_Guide/sec-Audit_Record_Types.html)

[2] [Estados SELinux](https://wiki.gentoo.org/wiki/SELinux/Tutorials/Permissive_versus_enforcing#Switching_between_enforcing_and_permissive)

[3] [Unconfined domains](https://wiki.gentoo.org/wiki/SELinux/Tutorials/What_is_this_unconfined_thingie_and_tell_me_about_attributes)

[4] [UBAC](http://blog.siphos.be/2011/05/selinux-user-based-access-control/)