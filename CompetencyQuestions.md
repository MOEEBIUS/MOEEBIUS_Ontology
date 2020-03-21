# Competency Questions

During the development of the MOEEBIUS ontology Competency Questions (CQ) have been defined to evaluate the developed terminological knowledge model. The competency questions have been formulated in 1. and a formal implementation is provided in this document. The formal implementation below has been tested on instances populated for the KUBIK building 2., a test building situated in Bilbao, Spain.

## Test setup

All queries have been tested on a instance of [GraphDB](http://www.ontotext.com). A clean data base has been created and OWL 2 RL reasoning profile was set active. The BrickFrame (v1.0.3), Brick (v1.0.3) and MOEEBIUSOntology (v0.0.4) have been loaded to the data base as well as the KUBIK Site instances (v0.0.4).

The prefixes in the queries are listed below.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX brick: <https://brickschema.org/schema/1.0.3/Brick#>
PREFIX bf: <https://brickschema.org/schema/1.0.3/BrickFrame#>
PREFIX om: <http://www.ontology-of-units-of-measure.org/vocabularies/om-2/>
PREFIX moeebius: <https://w3id.org/moeebius/MOEEBIUSOntology#>
PREFIX Abox: <https://w3id.org/moeebius/DemoSites#>
```

## CQ implemented in SPARQL

### Quickview

0. [Prefixes](#Prefixes)
1. [What are the temperature sensors in this storey?](#What-are-the-temperature-sensors-in-this-storey)
2. [What are the sensors in this room?](#What-are-the-sensors-in-this-room)
3. [Which final spaces are served by Boiler B?](#Which-final-spaces-are-served-by-Boiler-B)
4. [What is the ID of the temperature sensor in this room?](#What-is-the-ID-of-the-temperature-sensor-in-this-room)
5. [Which VRF or FCU supplies heat to this room?](#Which-VRF-or-FCU-supplies-heat-to-this-room)
6. [Which is the total energy meter of this building?](#Which-is-the-total-energy-meter-of-this-building)
7. [What is the ID of the active power sensor of this building?](#What-is-the-ID-of-the-active-power-sensor-of-this-building)
8. [What is the ID of the simulated value of the temperature in Room X?](#What-is-the-ID-of-the-simulated-value-of-the-temperature-in-Room-X)
9. [Which pieces of HVAC equipment are connected to VRF5 or VRF9?](#Which-pieces-of-HVAC-equipment-are-connected-to-VRF5-or-VRF9)
10. [Which rooms are served or fed by VFR5 or VRF 9?](#Which-rooms-are-served-or-fed-by-VFR5-or-VRF-9)
11. [Which pieces of HVAC equipment serve the zone which contains Room X?](#Which-pieces-of-HVAC-equipment-serve-the-zone-which-contains-Room-X)
12. [What is the cassandra ID or BEPS ID of point X?](#What-is-the-cassandra-ID-or-BEPS-ID-of-point-X)
13. [What HVAC equipment is installed in building X?](#What-HVAC-equipment-is-installed-in-building-X)
14. [What is the quantity and unit of a zone air temperature sensor in Room X?](#What-is-the-quantity-and-unit-of-a-zone-air-temperature-sensor-in-Room-X)

### What are the temperature sensors in this storey?

```sparql
PREFIX […]
SELECT ?Sensors
WHERE { 
    BIND( Abox:SI01.BL00.ST00 as ?Location )
    ?Location rdf:type brick:Floor .   
    ?Location bf:hasPart ?Room .
    ?Room bf:isLocationOf ?Sensors .
    ?Sensors rdf:type brick:Temperature_Sensor .
}
```

### What are the sensors in this room?


```sparql
PREFIX […]
SELECT ?Sensors
WHERE {
  BIND( Abox:SI01.BL00.ST00.SP01 as ?Location )
  ?Location rdf:type brick:Room .
  ?Location bf:isLocationOf ?Sensors .
  ?Sensors rdf:type brick:Sensor .
}
```

### Which final spaces are served by Boiler B?

```sparql
PREFIX […]
SELECT ?roomFedByBoiler
WHERE {
  BIND( Abox:SI01.BL00.CL00.SP00.BLR00  as ?boiler )
  ?boiler rdf:type brick:Boiler .
  ?boiler bf:feeds+ ?zone .
  ?zone rdf:type brick:HVAC_Zone .
  ?zone bf:hasPart ?roomFedByBoiler .
}
```

### What is the ID of the temperature sensor in this room?

```sparql
PREFIX […]
SELECT ?id
WHERE {
  BIND( Abox:SI01.BL00.ST00.SP01 as ?Location )
  ?Location rdf:type brick:Room .
  ?Location bf:isLocationOf ?Sensor .
  ?Sensor rdf:type brick:Zone_Air_Temperature_Sensor .
  ?Sensor moeebius:cassandraID ?id .
}

```

### Which VRF or FCU supplies heat to this room?

```sparql
PREFIX […]
SELECT ?VRForFCUFeedingRoom ?zone
WHERE {
BIND( Abox:SI01.BL00.CL00.SP00 as ?room )
?room bf:isPartOf ?zone .
?zone bf:isFedBy ?VRForFCUFeedingRoom .
OPTIONAL{ ?VRForFCUFeedingRoom rdf:type brick:FCU } .
OPTIONAL{ ?VRForFCUFeedingRoom rdf:type moeebius:VRF }.
}
```

### Which is the total energy meter of this building?

```sparql
PREFIX […]
SELECT ?tem
WHERE {
BIND( Abox:SI01.BL00 as ?Building )
?Building bf:hasPoint ?tem .
?tem rdf:type moeebius:Total_Energy_Electricity_Energy_Meter .
}
```

### What is the ID of the active power sensor of this building?

```sparql
PREFIX […]
SELECT ?tem
WHERE {
BIND( Abox:SI01.BL00 as ?Building )
?Building bf:hasPoint ?tem .
?tem rdf:type moeebius:Active_Electrical_Power_Meter .
}
```

### What is the ID of the simulated value of the temperature in Room X?

```sparql
PREFIX […]
SELECT ?id
WHERE {
BIND( Abox:SI01.BL00.ST00.SP01 as ?Location )
?Location rdf:type brick:Room .
?Location bf:isLocationOf ?Sensor .
?Sensor rdf:type brick:Zone_Air_Temperature_Sensor .
?Sensor moeebius:bepsID ?id .
}
```

### Which pieces of HVAC equipment are connected to VRF5 or VRF9?

```sparql
PREFIX […]
SELECT ?equipmentFedByVRF5
WHERE {
# Query fails for test set, as no VRF exists. Equal query for boiler works
# BIND(Abox:SI01.BL00.CL00.SP00.BLR00  as ?source )
BIND( :VRF5  as ?source )
?source bf:feeds+ ?equipmentFedByVRF5 .
?equipmentFedByVRF5 rdf:type brick:HVAC .
}
```

### Which rooms are served or fed by VFR5 or VRF 9?

```sparql
PREFIX […]
SELECT ?roomFedByEquipment
WHERE{
# Query fails as no VRF unit is present
# Logically equivalent query with boiler works:
# BIND( Abox:SI01.BL00.CL00.SP00.BLR00  as ?source )
# ?source rdf:type brick:Boiler .
BIND( :VRF5  as ?source )
?source rdf:type moeebius:VRF .
?source bf:feeds+ ?zone .
?zone rdf:type brick:HVAC_Zone .
?zone bf:hasPart ?roomFedByEquipment .
}
```

### Which pieces of HVAC equipment serve the zone which contains Room X?

```sparql
PREFIX […]
SELECT ?equipment
WHERE {
BIND( Abox:SI01.BL00.CL00.SP00 as ?room )
?room bf:isPartOf ?zone .
?zone rdf:type brick:HVAC_Zone .
?equipment bf:feeds+ ?zone .
?equipment rdf:type brick:HVAC .
}
```

### What is the cassandra ID or BEPS ID of point X?

```sparql
PREFIX […]
SELECT ?cassandraID ?bepsID
WHERE {
BIND( Abox:SI01.BL00.P00.M1.TI as ?point )
?point rdf:type brick:Point .
?point moeebius:cassandraID ?cassandraID .
?point moeebius:bepsID ?bepsID .
}
```

### What HVAC equipment is installed in building X?

```sparql
PREFIX […]
SELECT ?Equipment
WHERE {
BIND(  Abox:SI01.BL00 as ?building )
?building rdf:type brick:Building .
?building bf:hasPart ?location .
?location bf:isLocationOf ?Equipment .
?Equipment rdf:type brick:Equipment .
}
```

### What is the quantity and unit of a zone air temperature sensor in Room X?

```sparql
PREFIX […]
SELECT ?quantity ?unit
WHERE {
BIND(  Abox:SI01.BL00.ST00.SP01 as ?room )
?room bf:isLocationOf ?sensor .
?sensor rdf:type brick:Zone_Air_Temperature_Sensor.
?sensor moeebius:hasUnit ?unit .
?sensor moeebius:hasQuantity ?quantity
}
```

## References

1. G. D. Kontes, H. Qiu, L. Wang, M. Visser, G. F. Schneider, N. Panagiotidou, G. Grün, V. Sánchez, A. Romero-Amorrortu, P. de Agustin-Camacho, J. Rojicek, Z. Schindler, J. Malanik, A. Tsitsanis, T. Papapolyzos, C. Beder, M. Klepal, P. van Tooren, A. Nales, A. Stam, C. Malavazos, D6.8: MOEEBIUS Integrated DSS (Final Version), MOEEBIUS consortium, Brussels, Belgium (2018).
2. J. A. Chica, I. Apraiz, P. Elguezabal, M. O. Rrips, V. Sánchez, B. Tellado, Kubik: Open building approach for the construction of an unique experimental facility aimed to improve energy efficiency in buildings, open house international 36 (1) (2011) 63.
