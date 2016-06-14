# NBI-2001
# Sintaxis para el cálculo del índice NBI desarrollado por INEC para el censo 2010
# Fuente: http://www.ecuadorencifras.gob.ec/pobreza/ 
# Adaptado por: Victor Torres Lopez 
# Manual para adquisición de datos 
## https://victorhugotorreslopez.wordpress.com/2016/01/20/calculo-del-indice-de-necesidades-basicas-insatisfechas-en-ecuador-nbi/ 
## Publicado en Septiembre 2015 Actualizado: Enero 20.01.2016


RUNDEF programa  NBI SD 2010
   SELECTION  "Z:\01_gis_trabajo\001_TESIS_UNIGIS\SHP\Originales\2010\Tablas Excel\Seleccion de prov\Prov_tesis.sel"
   COMPLETENAME 
    UNIVERSE VIVIENDA.VTV <= 8 AND VIVIENDA.VCO = 1 
 

*/ INDICADOR DE NECESIDADES BÁSICAS INSATISFECHAS */    

/*  COMPONENTES: MATERIALES DE LA VIVIENDA DEFICITARIOS*/ 
   
DEFINE HOGAR.MATERIAL
    AS 1
    FOR ( VIVIENDA.V03 > 5 AND VIVIENDA.V03 <= 7 ) OR ( VIVIENDA.V05 > 5 AND VIVIENDA.V05 <= 7 )
    TYPE INTEGER
    RANGE 0-1
    VARLABEL "MATERIALES DE LA VIVIENDA DEFICITARIOS"
    VALUELABELS
    1 " Hogares con materiales de vivienda deficitarios"
    0 " Hogares con materiales de vivienda no deficitarios"
    DEFAULT 0
   /* 
       
    TABLE MATERIAL
    TITLE "MATERIAL DE LA VIVIENDA DEFICITARIOS"
    AS FREQUENCY 
    OF HOGAR.MATERIAL
    COMPLETENAME 
 */       
     
/* COMPONENTE: SERVICIOS DE LA VIVIENDA INADECUADOS*/ 
            
 DEFINE HOGAR.SERVICIO
    AS 1
    FOR ( VIVIENDA.V07 > 1 AND VIVIENDA.V07 <= 5 ) OR ( VIVIENDA.V08 > 1 AND VIVIENDA.V08 <= 4 ) OR ( VIVIENDA.V09 > 2 AND VIVIENDA.V09 <= 6 )
    TYPE INTEGER
    RANGE 0-1
    VARLABEL "SERVICIOS DE LA VIVIENDA INADECUADOS"
    VALUELABELS
    1 " Hogares con servicios de las viviendas inadecuados"
    0 " Hogares con servicios adecuados"
    DEFAULT 0
  /*  
        
    TABLE SERVICIOS
    TITLE " SERVICIOS DE LA VIVIENDA INADECUADOS"
    AS FREQUENCY 
    OF HOGAR.SERVICIO   
    COMPLETENAME 
    
 */
    
/* COMPONENTE HACINAMIENTO  */ 
 
       
    DEFINE HOGAR.perxdor
    AS SWITCH
    INCASE HOGAR.H01 >  0
    ASSIGN HOGAR.TOTPER / HOGAR.H01
    INCASE HOGAR.H01 = 0
    ASSIGN HOGAR.TOTPER
    TYPE REAL

    DEFINE HOGAR.defhacin
    AS SWITCH
    INCASE HOGAR.H01 > 0  AND HOGAR.perxdor <= 3
    ASSIGN 0
    INCASE HOGAR.H01 > 0  AND HOGAR.perxdor > 3
    ASSIGN 1
    INCASE HOGAR.H01 =0 AND HOGAR.TOTPER <=3
    ASSIGN 0
    INCASE HOGAR.H01 =0 AND HOGAR.TOTPER >3
    ASSIGN 1
    TYPE INTEGER 
    RANGE 0-1
    VALUELABELS
    1 "HOGARES CON HACINAMIENTO"
    0 "HOGARES SIN HACINAMIENTO"
    


/*
TABLE HACINAMIENTO
AS FREQUENCY 
OF HOGAR.defhacin
    
*/    
    
*/ COMP0NENTE : NIÑOS DE 6 A 12 AÑOS QUE NO ASISTEN A CLASES */ 
            
DEFINE HOGAR.NINNOASIS
    AS 1
    FOR ( PERSONA.P03 > 5 AND PERSONA.P03 <= 12 ) AND PERSONA.P21 = 2 
    TYPE INTEGER
    RANGE 0-1
    VARLABEL "NIÑOS NO ASISTEN"
    VALUELABELS
    1 " Hogares con niños que no asisten a la escuela"
    0 " Hogares  sin niños que no asistan a la escuela"
    DEFAULT 0
    
 /*
    TABLE NINOASIS
    TITLE " NIÑOS ENTRE 6 Y 12 AÑOS QUE NO ASISTEN A ESCUELA"
    AS FREQUENCY 
    OF HOGAR.NINNOASIS
    COMPLETENAME    
*/    
                              
    
/* COMPONENTE: DEPENDENCIA ECONOMICA */ 
                   
/* CONTANDO OCUPADOS DE 10 AÑOS Y MÁS*/

DEFINE HOGAR.OCU
    AS COUNT PERSONA
    TYPE INTEGER
    FOR  PERSONA.P03 >= 10 AND PERSONA.TIPOACT < 6  

   
    
/* CALCULADO COCIENTE  TOTAL MIEMBROS DE HOGAR PARA OCUPADOS EN EL HOGAR*/ 
          
DEFINE HOGAR.PEROCU
    AS HOGAR.TOTPER / HOGAR.OCU 
    TYPE REAL
    FOR HOGAR.OCU >0 
         
                     
/* CALCULANDO  HOGARES PROMEDIO DE 3 O MAS MIEMBROS DEL HOGAR POR CADA OCUPADO */ 

DEFINE HOGAR.MIXOCU1
    AS SWITCH
    INCASE HOGAR.PEROCU > 3
    ASSIGN 1
    INCASE HOGAR.OCU = 0
    ASSIGN 1
    RANGE 0-1
    TYPE INTEGER
    DEFAULT 0 
    
    **** Años de Escolaridad**

DEFINE PERSONA.DIST
    AS PERSONA.P23 * 100 + PERSONA.P24
    TYPE INTEGER
    FOR PERSONA.P23 < 99 
    
DEFINE PERSONA.ESCONBI
    AS SWITCH
    INCASE PERSONA.P23 = 1
    ASSIGN 0
    INCASE PERSONA.DIST = 201 AND PERSONA.P21 = 1
    ASSIGN 0
    INCASE PERSONA.DIST = 201 AND PERSONA.P21 = 2
    ASSIGN 3
    INCASE PERSONA.DIST = 202 AND PERSONA.P21 = 1
    ASSIGN 3
    INCASE PERSONA.DIST = 202 AND PERSONA.P21 = 2
    ASSIGN 5
    INCASE PERSONA.DIST = 203 AND PERSONA.P21 = 1
    ASSIGN 5
    INCASE PERSONA.DIST = 203 AND PERSONA.P21 = 2
    ASSIGN 7
    INCASE PERSONA.DIST = 301 AND PERSONA.P21 = 1
    ASSIGN 0
    INCASE PERSONA.DIST = 301 AND PERSONA.P21 = 2
    ASSIGN 1
    INCASE PERSONA.DIST = 401 AND PERSONA.P21 = 1
    ASSIGN 1
    INCASE PERSONA.DIST = 401 AND PERSONA.P21 = 2
    ASSIGN 2
    INCASE PERSONA.DIST = 402 AND PERSONA.P21 = 1
    ASSIGN 2
    INCASE PERSONA.DIST = 402 AND PERSONA.P21 = 2
    ASSIGN 3
    INCASE PERSONA.DIST = 403 AND PERSONA.P21 = 1
    ASSIGN 3
    INCASE PERSONA.DIST = 403 AND PERSONA.P21 = 2
    ASSIGN 4
    INCASE PERSONA.DIST = 404 AND PERSONA.P21 = 1
    ASSIGN 4
    INCASE PERSONA.DIST = 404 AND PERSONA.P21 = 2
    ASSIGN 5
    INCASE PERSONA.DIST = 405 AND PERSONA.P21 = 1
    ASSIGN 5
    INCASE PERSONA.DIST = 405 AND PERSONA.P21 = 2
    ASSIGN 6
    INCASE PERSONA.DIST = 406 AND PERSONA.P21 = 1
    ASSIGN 6
    INCASE PERSONA.DIST = 406 AND PERSONA.P21 = 2
    ASSIGN 7
    INCASE PERSONA.DIST = 501 AND PERSONA.P21 = 1
    ASSIGN 7
    INCASE PERSONA.DIST = 501 AND PERSONA.P21 = 2
    ASSIGN 8
    INCASE PERSONA.DIST = 502 AND PERSONA.P21 = 1
    ASSIGN 8
    INCASE PERSONA.DIST = 502 AND PERSONA.P21 = 2
    ASSIGN 9
    INCASE PERSONA.DIST = 503 AND PERSONA.P21 = 1
    ASSIGN 9
    INCASE PERSONA.DIST = 503 AND PERSONA.P21 = 2
    ASSIGN 10
    INCASE PERSONA.DIST = 504 AND PERSONA.P21 = 1
    ASSIGN 10
    INCASE PERSONA.DIST = 504 AND PERSONA.P21 = 2
    ASSIGN 11
    INCASE PERSONA.DIST = 505 AND PERSONA.P21 = 1
    ASSIGN 11
    INCASE PERSONA.DIST = 505 AND PERSONA.P21 = 2
    ASSIGN 12
    INCASE PERSONA.DIST = 506 AND PERSONA.P21 = 1
    ASSIGN 12
    INCASE PERSONA.DIST = 506 AND PERSONA.P21 = 2
    ASSIGN 13
    INCASE PERSONA.DIST = 601 AND PERSONA.P21 = 1
    ASSIGN 0
    INCASE PERSONA.DIST = 601 AND PERSONA.P21 = 2
    ASSIGN 1
    INCASE PERSONA.DIST = 602 AND PERSONA.P21 = 1
    ASSIGN 1
    INCASE PERSONA.DIST = 602 AND PERSONA.P21 = 2
    ASSIGN 2
    INCASE PERSONA.DIST = 603 AND PERSONA.P21 = 1
    ASSIGN 2
    INCASE PERSONA.DIST = 603 AND PERSONA.P21 = 2
    ASSIGN 3
    INCASE PERSONA.DIST = 604 AND PERSONA.P21 = 1
    ASSIGN 3
    INCASE PERSONA.DIST = 604 AND PERSONA.P21 = 2
    ASSIGN 4
    INCASE PERSONA.DIST = 605 AND PERSONA.P21 = 1
    ASSIGN 4
    INCASE PERSONA.DIST = 605 AND PERSONA.P21 = 2
    ASSIGN 5
    INCASE PERSONA.DIST = 606 AND PERSONA.P21 = 1
    ASSIGN 5
    INCASE PERSONA.DIST = 606 AND PERSONA.P21 = 2
    ASSIGN 6
    INCASE PERSONA.DIST = 607 AND PERSONA.P21 = 1
    ASSIGN 6
    INCASE PERSONA.DIST = 607 AND PERSONA.P21 = 2
    ASSIGN 7
    INCASE PERSONA.DIST = 608 AND PERSONA.P21 = 1
    ASSIGN 7
    INCASE PERSONA.DIST = 608 AND PERSONA.P21 = 2
    ASSIGN 8
    INCASE PERSONA.DIST = 609 AND PERSONA.P21 = 1
    ASSIGN 8
    INCASE PERSONA.DIST = 609 AND PERSONA.P21 = 2
    ASSIGN 9
    INCASE PERSONA.DIST = 610 AND PERSONA.P21 = 1
    ASSIGN 9
    INCASE PERSONA.DIST = 610 AND PERSONA.P21 = 2
    ASSIGN 10
    INCASE PERSONA.DIST = 701 AND PERSONA.P21 = 1
    ASSIGN 10
    INCASE PERSONA.DIST = 701 AND PERSONA.P21 = 2
    ASSIGN 11
    INCASE PERSONA.DIST = 702 AND PERSONA.P21 = 1
    ASSIGN 11
    INCASE PERSONA.DIST = 702 AND PERSONA.P21 = 2
    ASSIGN 12
    INCASE PERSONA.DIST = 703 AND PERSONA.P21 = 1
    ASSIGN 12
    INCASE PERSONA.DIST = 703 AND PERSONA.P21 = 2
    ASSIGN 13
    INCASE PERSONA.DIST = 801 AND PERSONA.P21 = 1
    ASSIGN 13
    INCASE PERSONA.DIST = 801 AND PERSONA.P21 = 2
    ASSIGN 14
    INCASE PERSONA.DIST = 802 AND PERSONA.P21 = 1
    ASSIGN 14
    INCASE PERSONA.DIST = 802 AND PERSONA.P21 = 2
    ASSIGN 15
    INCASE PERSONA.DIST = 803 AND PERSONA.P21 = 1
    ASSIGN 15
    INCASE PERSONA.DIST = 803 AND PERSONA.P21 = 2
    ASSIGN 16
    INCASE PERSONA.DIST = 901 AND PERSONA.P21 = 1
    ASSIGN 13
    INCASE PERSONA.DIST = 901 AND PERSONA.P21 = 2
    ASSIGN 14
    INCASE PERSONA.DIST = 902 AND PERSONA.P21 = 1
    ASSIGN 14
    INCASE PERSONA.DIST = 902 AND PERSONA.P21 = 2
    ASSIGN 15
    INCASE PERSONA.DIST = 903 AND PERSONA.P21 = 1
    ASSIGN 15
    INCASE PERSONA.DIST = 903 AND PERSONA.P21 = 2
    ASSIGN 16
    INCASE PERSONA.DIST = 904 AND PERSONA.P21 = 1
    ASSIGN 16
    INCASE PERSONA.DIST = 904 AND PERSONA.P21 = 2
    ASSIGN 17
    INCASE PERSONA.DIST = 905 AND PERSONA.P21 = 1
    ASSIGN 17
    INCASE PERSONA.DIST = 905 AND PERSONA.P21 = 2
    ASSIGN 18
    INCASE PERSONA.DIST = 906 AND PERSONA.P21 = 1
    ASSIGN 18
    INCASE PERSONA.DIST = 906 AND PERSONA.P21 = 2
    ASSIGN 19
    INCASE PERSONA.DIST = 907 AND PERSONA.P21 = 1
    ASSIGN 19
    INCASE PERSONA.DIST = 907 AND PERSONA.P21 = 2
    ASSIGN 20
    INCASE PERSONA.DIST = 908 AND PERSONA.P21 = 1
    ASSIGN 20
    INCASE PERSONA.DIST = 908 AND PERSONA.P21 = 2
    ASSIGN 21
    INCASE PERSONA.DIST = 1001 AND PERSONA.P21 = 1
    ASSIGN 18
    INCASE PERSONA.DIST = 1001 AND PERSONA.P21 = 2
    ASSIGN 19
    INCASE PERSONA.DIST = 1002 AND PERSONA.P21 = 1
    ASSIGN 19
    INCASE PERSONA.DIST = 1002 AND PERSONA.P21 = 2
    ASSIGN 20
    INCASE PERSONA.DIST = 1003 AND PERSONA.P21 = 1
    ASSIGN 20
    INCASE PERSONA.DIST = 1003 AND PERSONA.P21 = 2
    ASSIGN 21
    INCASE PERSONA.DIST = 1004 AND PERSONA.P21 = 1
    ASSIGN 21
    INCASE PERSONA.DIST = 1004 AND PERSONA.P21 = 2
    ASSIGN 22
    INCASE PERSONA.DIST = 1005 AND PERSONA.P21 = 1
    ASSIGN 22
    INCASE PERSONA.DIST = 1005 AND PERSONA.P21 = 2
    ASSIGN 23
    INCASE PERSONA.DIST = 1006 AND PERSONA.P21 = 1
    ASSIGN 23
    INCASE PERSONA.DIST = 1006 AND PERSONA.P21 = 2
    ASSIGN 24
    INCASE PERSONA.P23 = 5 AND PERSONA.P24 = 99
    ASSIGN 12
    INCASE PERSONA.P23 >= 7 AND PERSONA.P24 = 99
    ASSIGN 12
    TYPE INTEGER
    FOR PERSONA.DIST < 99999 
    VARLABEL "ESCOLARIDAD NBI"
    RANGE 0-24
   
            
        
/*C0NTANDO JEFES DE HOGAR CON EDUCACIÓN HASTA 2DO GRADO DE ESCUELA. CALCULO CON ESCOLARIDAD SISTEMA VIGENTE SON 3 AÑOS */
 
DEFINE HOGAR.JEFEDU1
    AS SWITCH
    INCASE PERSONA.P02 = 1 AND PERSONA.ESCONBI > 3
    ASSIGN 0
    INCASE PERSONA.P02 = 1 AND PERSONA.ESCONBI <= 3
    ASSIGN 1
    INCASE PERSONA.P02 = 1 AND PERSONA.P23 = 1
    ASSIGN 1
    TYPE INTEGER
    RANGE 0-9
    VALUELABELS
    9 "no clasificado"
        MISSING 9
        
        
/*  DEPENDENCIA (HOGARES CON MÁS DE 3 MIEMBROS Y JEFE APROBADO MÁXIMO 2 AÑOS */ 
                         
DEFINE HOGAR.DEPEND1
    AS SWITCH
    INCASE HOGAR.MIXOCU1 = 1 AND HOGAR.JEFEDU1 = 1
    ASSIGN 1
    INCASE HOGAR.MIXOCU1 = 1 AND HOGAR.JEFEDU1 = 0
    ASSIGN 0
    INCASE HOGAR.MIXOCU1 = 1 AND HOGAR.JEFEDU1 = 9
    ASSIGN 9
    INCASE HOGAR.MIXOCU1 = 0 AND HOGAR.JEFEDU1 = 1
    ASSIGN 0
    INCASE HOGAR.MIXOCU1 = 0 AND HOGAR.JEFEDU1 = 0
    ASSIGN 0
    INCASE HOGAR.MIXOCU1 = 0 AND HOGAR.JEFEDU1 = 9
    ASSIGN 9
    TYPE INTEGER
    RANGE 0-9
    VARLABEL "DEPENDENCIA"
    VALUELABELS
    0 " Hogares sin dependencia económica"
    1 "Hogares con dependencia económica" 
    9 "no clasificado"

 /*                   
TABLE DEPEND1
        TITLE "DEPENDENCIA ECONÓMICA"
    AS FREQUENCY 
    OF HOGAR.DEPEND1
        COMPLETENAME
   */
                            
******************************** CALCULANDO NBI**************************************
    
DEFINE HOGAR.NBI1
    AS SWITCH
    INCASE HOGAR.DEPEND1 < 9
    ASSIGN HOGAR.MATERIAL + HOGAR.SERVICIO + HOGAR.defhacin + HOGAR.NINNOASIS + HOGAR.DEPEND1
    INCASE HOGAR.DEPEND1 = 9
    ASSIGN HOGAR.MATERIAL + HOGAR.SERVICIO + HOGAR.defhacin + HOGAR.NINNOASIS
    TYPE INTEGER
    RANGE 0-9
    VALUELABELS
    9 "no clasificado"
    

*DISTRIBUCION NBI* 
  
DEFINE HOGAR.NBIFIN
    AS SWITCH
    INCASE HOGAR.DEPEND1 = 9 AND HOGAR.NBI1 >= 1
    ASSIGN 1
    INCASE HOGAR.NBI1 = 0 AND HOGAR.DEPEND1 < 9
    ASSIGN 0
    INCASE HOGAR.NBI1 = 1 AND HOGAR.DEPEND1 < 9
    ASSIGN 1
    INCASE HOGAR.NBI1 >= 2 AND HOGAR.DEPEND1 < 9
    ASSIGN 1
    TYPE INTEGER
    VARLABEL "HOGARES SEGUN NIVEL DE POBREZA"
    VALUELABELS
    1 "HOGARES POBRES"
    0 "HOGARES NO POBRES"
    RANGE 0-1
    
TABLE TABLE584
    AS FREQUENCY 
    OF HOGAR.NBIFIN
    TALLY HOGAR.TOTPER
     
TABLE PROVIN AS AREALIST OF PROVIN, PROVIN.NOMPROV, HOGAR.NBIFIN
TALLY HOGAR.TOTPER
TOTAL

TABLE CANT AS AREALIST OF CANTON, CANTON.NOMCANT, HOGAR.NBIFIN
TALLY HOGAR.TOTPER
TOTAL

TABLE PARR AS AREALIST OF PARROQ, PARROQ.NOMPARR, HOGAR.NBIFIN
TALLY HOGAR.TOTPER
TOTAL

TABLE ZON AS AREALIST OF ZONA, ZONA.IDZONA, HOGAR.NBIFIN
TALLY HOGAR.TOTPER
TOTAL

TABLE SEC AS AREALIST OF SECTOR, SECTOR.IDSECTOR, HOGAR.NBIFIN
TALLY HOGAR.TOTPER
TOTAL
*/    
