## 1. Vamos a crear las credenciales 

Es necesario crear las credenciales utilizando el paquete DBMS_CLOUD y la función CREATE_CREDENTIAL.
Esto nos permite dar a la base de datos los permisos de utilizar algunos recursos de nuestro compartment.


```sql
BEGIN
DBMS_CLOUD.CREATE_CREDENTIAL(
    credential_name => 'GENAI_CRED',
    user_ocid       => 'ocid1.user...',
    tenancy_ocid    => 'ocid1.tenancy...',
    private_key     => '',
    fingerprint     => '6d:23:aa:cf:a3:f4'
  );
END;
```

## 2. Vamos a crear el perfil de IA

Esto nos permite indicarle a la base de datos cuál modelo de IA utilizar, además nos permite especificar las tablas a las que se tendrán acceso.

Algunos links útiles para el siguiente comando

[Obtener mi compartment](https://cloud.oracle.com/identity/compartments)
[Obtener mi región](https://cloud.oracle.com/regions)


```sql
BEGIN                                                                        
  DBMS_CLOUD_AI.CREATE_PROFILE(                                              
      profile_name => 'GENAI',                                                             
      attributes => '{"provider": "oci",                                                                   
        "credential_name": "GENAI_CRED",                                     
        "object_list": [{"owner": "ADMIN", "name": "PLAYER_STATS"}, {"owner": "ADMIN", "name": "PL_TEAMS_2025"}],
                "oci_compartment_id": "ocid1.tenancy...",
                "region": "la región en la que nos encontramos",
                "model": "meta.llama-3.3-70b-instruct",
                "comments":"true"      
       }');                                                                  
END;                                                                                                          ```
```

## Preguntemos a la base de datos

Vamos a realizar pruebas utilizando narrate, aunque es posible con _explainsql_, _showsql_, _runsql_.

```sql
SELECT DBMS_CLOUD_AI.GENERATE(prompt => 'quién es el mejor jugador?',
profile_name => 'GENAI',
action       => 'narrate')
FROM dual;
```

## Algunos comandos útiles

Borrar las credenciales, creadas, esto es útil para volver a crear las credenciales de nuevo.

```sql
EXEC DBMS_CREDENTIAL.DROP_CREDENTIAL('GENAI_CRED', FALSE);
```

Eliminar el perfil de IA creado, esto es útil para crear el perfil nuevamente.
```sql
BEGIN
     DBMS_CLOUD_AI.DROP_PROFILE(profile_name => 'GENAI');
END;
```