# HomeAssistant_Saveeye
SAVEEYE+ FOR ECHELON<br>
Den pdf der ligger her er lavet af SaveEye i samarbejde med en af deres kunder "Christoffer"
<br>
## Forord
Har samlet lidt om opsætning af<br>
Jeg har efter lang tid, fået min SaveEye til at virke med HomeAssistant MQTT. <br>
Det har været en rejse jeg gerne vil se om jeg kan spare andre for<br><br>

## Udfordringer
Første udfordring var at jeg skulle have data ud af enheden, der er ikke mange informationer<br>
fra den pdf MQTT som SaveEye kan sende ud hvis man har udfordringer, (Den ligger også her)<br><br>

### Password:
Det er vigtigt at man laver et password til sin MQTT broker der er under 25 tegn (ingen specialtegn).<br>
Dette kan gøres ved at oprette en ny bruger på sin Home Assistant, da alle bruger har adgang til MQTT.<br>
Når du så har lavet en ny bruger (dette gøres i /config/users) kan du lave en bruger der kun har<br>
adgang fra lokalt netværk, denne kan så sættes på i SaveEye appen på din smartphone/tablet<br>
Nu kan din SaveEye enhed komme i kontakt med din MQTT broker<br><br>
### Test:
Hvis du gerne vil vide om der kommer data fra SaveEye, kan du med fordel hente et lille program<br>
der hedder "mqtt explorer" (se links sektionen) med denne kan du også se hvilken data den sender<br>
### Data:
Dette er de data strenge der kommer med over fra SaveEye enheden, det næste er at omsætte det<br>
til noget vi faktisk kan bruge.<br>
			
				saveeyeDeviceSerialNumber
				meterType
				meterSerialNumber
				timestamp
				activeActualConsumption
						total
				activeActualProduction
						total
				activeTotalConsumption
						total
				activeTotalProduction
						total
				reactiveActualConsumption
						total
				reactiveActualProduction
						total
				rmsVoltage
					L1
					L2
					L3
				rmsCurrent
					L1
					L2
					L3
				powerFactor
					L1
					L2
					L3
## MQTT
For at få de nødvendige data ind i vores MQTT, skal vi have dem tilføjet manuelt. 
Dette gøres ved at redigere filen "configuration.yaml". 
Denne finder du enten i den SAMBA Share eller ved at installere et AddOn "Studio Code Server",<br>
her kan du redigere filen og efterfølgene teste om din konfikuration er korekt.<br><br>

## Sensor entity:
				device_class: VOLTAGE
				unit_of_measurement: V
Der ligger et link længere nede der forklare "Sensor entity" hvilken type af sensor der kan bruges i de ovennævnte.<br>
## YAML

	template:
		sensor:
			- name: "saveeye"
			  unit_of_measurement: W
			  state: >
			        {{ 3600000000 / (states('sensor.saveeye_latestInterval') | float(0) * 1000)}}

Dette er den første del af enheden, her fortæller vi hvad det er for en sensor, og hvilken parametre den har<br>

		mqtt:
			sensor:
			  - name: saveeyeDeviceSerialNumber
				unique_id: saveeyeDeviceSerialNumber
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.saveeyeDeviceSerialNumber}}"
			  - name: meterType
				unique_id: meterType
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.meterType}}"
			  - name: meterSerialNumber
				unique_id: meterSerialNumber
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.meterSerialNumber}}"
			  - name: timestamp
				unique_id: timestamp
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.timestamp}}"
			  - name: activeActualConsumption
				unique_id: activeActualConsumption
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.activeActualConsumption.total}}"
				device_class: power
				unit_of_measurement: W
				state_class: measurement
			  - name: activeActualProduction
				unique_id: activeActualProduction
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.activeActualProduction.total}}"
				device_class: power
				unit_of_measurement: W
				state_class: measurement
			  - name: activeTotalConsumption
				unique_id: activeTotalConsumption
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.activeTotalConsumption.total}}"
				device_class: ENERGY
				unit_of_measurement: Wh
				state_class: total_increasing
			  - name: activeTotalProduction
				unique_id: activeTotalProduction
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.activeTotalProduction.total}}"
				device_class: ENERGY
				unit_of_measurement: Wh
				state_class: total_increasing
			  - name: reactiveActualConsumption
				unique_id: reactiveActualConsumption
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.reactiveActualConsumption.total}}"
				device_class: power
				unit_of_measurement: W
				state_class: measurement
			  - name: reactiveActualProduction
				unique_id: reactiveActualProduction
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.reactiveActualProduction.total}}"
				device_class: power
				unit_of_measurement: W
				state_class: measurement
			  - name: rmsCurrentL1
				unique_id: rmsCurrentL1
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsCurrent.L1}}"
				device_class: CURRENT
				unit_of_measurement: mA
				state_class: measurement
			  - name: rmsCurrentL2
				unique_id: rmsCurrentL2
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsCurrent.L2}}"
				device_class: CURRENT
				unit_of_measurement: mA
				state_class: measurement
			  - name: rmsCurrentL3
				unique_id: rmsCurrentL3
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsCurrent.L3}}"
				device_class: CURRENT
				unit_of_measurement: mA
				state_class: measurement
			  - name: powerFactorL1
				unique_id: powerFactorL1
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.powerFactor.L1}}"
				device_class: POWER_FACTOR
				unit_of_measurement: "%"
				state_class: measurement
			  - name: powerFactorL2
				unique_id: powerFactorL2
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.powerFactor.L2}}"
				device_class: POWER_FACTOR
				unit_of_measurement: "%"
				state_class: measurement
			  - name: powerFactorL3
				unique_id: powerFactorL3
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.powerFactor.L3}}"
				device_class: POWER_FACTOR
				unit_of_measurement: "%"
				state_class: measurement
			  - name: rmsVoltageL1
				unique_id: rmsVoltageL1
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsVoltage.L1}}"
				device_class: VOLTAGE
				unit_of_measurement: V
				state_class: measurement
			  - name: rmsVoltageL2
				unique_id: rmsVoltageL2
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsVoltage.L2}}"
				device_class: VOLTAGE
				unit_of_measurement: V
				state_class: measurement
			  - name: rmsVoltageL3
				unique_id: rmsVoltageL3
				state_topic: "saveeye/telemetry"
				value_template: "{{value_json.rmsVoltage.L3}}"
				device_class: VOLTAGE
				unit_of_measurement: V
				state_class: measurement

				
## Links <br>
	- [MQTT Explore]  - (https://mqtt-explorer.com/) <br>
	- [SaveEye]       - (https://saveeye.dk/produkt/saveeye-for-echelon/) <br>
	- [Sensor entity] - (https://developers.home-assistant.io/docs/core/entity/sensor/#available-state-classes) <br>
	
