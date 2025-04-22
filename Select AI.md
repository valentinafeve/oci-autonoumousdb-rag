## 1. Vamos a crear las credenciales 

Es necesario crear las credenciales utilizando el paquete DBMS_CLOUD y la función CREATE_CREDENTIAL

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

Algunos links útiles 

[Obtener mi compartment](https://cloud.oracle.com/identity/compartments?region=null)
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
END;                                                                                                            ``` 

```sql
SELECT DBMS_CLOUD_AI.GENERATE(prompt       => 'quién es el mejor jugador?',
profile_name => 'GENAI',
action       => 'narrate')
FROM dual;
```

## Algunos comandos útiles

```sql
EXEC DBMS_CREDENTIAL.DROP_CREDENTIAL('GENAI_CRED', FALSE);
```

```sql
BEGIN
     DBMS_CLOUD_AI.DROP_PROFILE(profile_name => 'GENAI');
END;
```