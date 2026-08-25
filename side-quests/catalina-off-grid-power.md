# Catalina 134BHX Off-Grid Power

**Status:** Planning and phased implementation

This sidequest documents a phased electrical upgrade for the Coachmen Catalina Summit Series 7 134BHX. The design goal is to preserve normal shore-power and tow-vehicle operation while adding enough battery, inverter, solar, and generator capability to support meaningful off-grid use, including limited operation of the factory 13,500 BTU A/C and heat pump.

The trailer is factory pre-wired for Go Power solar. The identified prep location is labeled for a maximum 30 amps of solar and is intended for the Go Power/Dometic controller installation.

## Design baseline

The planned system uses three independent charging sources feeding a common 12 V LiFePO4 house bank:

- Roof solar through a 30 A MPPT controller
- 120 V shore power or portable generator through an inverter/charger
- Tow-vehicle charging, retained initially and optionally upgraded to controlled DC-DC charging

The battery bank then supplies the existing 12 V trailer loads directly and the 120 V system through a 3,000 W pure-sine inverter/charger.

Existing equipment already available:

- Coachmen Catalina Summit Series 7 134BHX
- GE 13,500 BTU A/C with heat pump
- RYOBI RYI2022VNM inverter generator, 2,050 W starting / 1,650 W running
- Hughes Power Watchdog WPC30A
- Factory 30 A shore-power service
- Factory tow-vehicle charging circuit
- Factory Go Power Wired for Solar prep

## Solar controller limits

The Go Power GP-RVC-30-MPPT supplied with the Eclipse system is the design constraint for the initial solar array:

- System voltage: 12/24 V
- Rated charge current: 30 A
- Maximum PV input voltage: less than 100 V
- Maximum PV input power: 600 W at 12 V, 1,200 W at 24 V
- Battery profiles include LiFePO4
- RS485 and CAN bus communication

The 200 W rigid Eclipse panel is rated at 20.4 Vmp, 9.6 A Imp, and 24.09 Voc. The intended 600 W configuration is three matching 200 W panels while remaining within the controller's documented 600 W limit at 12 V.

## Purchase and installation phases

Prices below are planning references captured in August 2026 and should be rechecked before purchase.

### Phase 0: Existing equipment

No purchase required.

| Component | Model | Reference price | Link | Purpose |
| --- | --- | ---: | --- | --- |
| Inverter generator | RYOBI RYI2022VNM | Already owned | https://www.directtoolsoutlet.com/products/RYI2022VNM | Generator backup and bulk charging source |
| 30 A electrical protection | Hughes Power Watchdog WPC30A | Already owned | https://hughesautoformers.com/product/pwd30-epo/ | Shore/generator input monitoring and protection |
| Factory solar prep | Go Power / Dometic Wired for Solar | Installed | https://gopowersolar.com/wired-for-solar/ | Existing solar wiring and controller location |

### Phase 1: Base solar

Install the supported Go Power Eclipse kit using the factory solar prep. This provides immediate battery maintenance and establishes the permanent MPPT infrastructure.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 200 W rigid Eclipse solar kit | Go Power GP-ECLIPSE-200 / 83315 | 1 | $631.99 | https://gopowersolar.com/products/rigid-eclipse-solar-kit/ |

The base kit includes the rigid 200 W panel, GP-RVC-30-MPPT controller, controller remote/monitoring hardware, solar cabling, and mounting hardware.

**Phase total:** approximately $632

### Phase 2: Battery bank

Replace the dealer-installed marine battery with a single high-capacity LiFePO4 bank. Preferred installation is low and structurally secured in the storage cubby if practical. A protected tongue installation remains an alternative if weight distribution and environmental protection are acceptable.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 12.8 V 600 Ah LiFePO4 RV battery | Vatrer D-12V-600AH, 300 A BMS | 1 | $1,259.99 | https://www.vatrerpower.com/products/vatrer-12v-600ah-bluetooth-lithium-rv-battery |

Nominal storage is 7.68 kWh. The 300 A continuous BMS is appropriately sized for the planned 3,000 W inverter class without requiring parallel batteries solely for discharge-current capacity.

**Cumulative planned hardware:** approximately $1,892

### Phase 3: Inverter, shore charging, and high-current DC distribution

Install the inverter/charger close to the battery bank to minimize the high-current 12 V cable run. This phase converts the battery system into a whole-trailer AC source and provides high-rate charging whenever shore or generator AC is available.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 3,000 W advanced inverter/charger | Go Power AIC-3000-12-SL | 1 | $1,419.99 | https://gopowersolar.com/products/aic-series-3000-watt-sl-advanced-inverter-charger/ |
| 4/0 inverter installation kit | Go Power GP-DC-KIT5 | 1 | $535.99 | https://gopowersolar.com/products/inverter-installation-kits/ |

The AIC-3000-12-SL provides the core integration point for 120 V operation: 3,000 W inverter capacity, shore/generator charging, and transfer functionality for the trailer's 30 A service. Charging current should be configured for the battery and available AC source rather than assuming maximum charger output in every operating mode.

The DC installation should include appropriately rated battery disconnect, Class T protection, high-current positive and negative distribution, and mechanical battery restraint. Exact ancillary hardware should be finalized from measured cable lengths and the selected battery/inverter mounting positions rather than purchased generically in advance.

**Cumulative planned hardware:** approximately $3,848

### Phase 4: Solar expansion to 400 W

Add one matching Eclipse expansion panel after the base system has been proven in service.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 200 W rigid Eclipse expansion | Go Power GP-ECLIPSE-200-E / 83316 | 1 | $279.99 | https://gopowersolar.com/products/rigid-eclipse-solar-kit/ |

**Array:** 400 W nominal  
**Cumulative planned hardware:** approximately $4,128

### Phase 5: Solar expansion to controller maximum

Add the third matching panel if roof layout and measured system performance justify it.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 200 W rigid Eclipse expansion | Go Power GP-ECLIPSE-200-E / 83316 | 1 | $279.99 | https://gopowersolar.com/products/rigid-eclipse-solar-kit/ |

**Final array:** 600 W nominal  
**Cumulative planned hardware:** approximately $4,408

This reaches the GP-RVC-30-MPPT's documented 600 W PV input rating for a 12 V battery system. Additional roof solar would require another/stacked controller or a redesigned solar subsystem rather than simply attaching more panels to this controller.

### Phase 6: A/C startup optimization

Add a soft starter matched to the installed GE 13,500 BTU rooftop unit before treating either the 3,000 W inverter or the 1,650 W continuous-output RYOBI as routine A/C sources.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| RV A/C soft starter | Micro-Air EasyStart Breeze, exact application model TBD after confirming GE A/C model | 1 | Verify at purchase | https://www.micro-air.com/products_easystart_399_softstarter_microair.cfm |

The exact soft-start model should be selected from the air conditioner's actual model and electrical data plate rather than inferred only from the 13,500 BTU rating.

### Phase 7: Optional tow-vehicle charging upgrade

The existing 7-pin charge feed can remain part of the trailer system, but high-current lithium charging should not be assumed from the factory wiring. If towing charge performance is inadequate, add a controlled DC-DC charger and size its input current to the actual vehicle/trailer wiring.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| Adjustable DC-DC charger | Victron Orion XS 12/12-50A, ORI121217050 | 1 | $349 MSRP reference | https://www.victronenergy.com/dc-dc-converters/orion-xs-dc-dc-battery-chargers |

The Orion XS can be current-limited below its 50 A maximum. A dedicated higher-current vehicle feed should use appropriately sized conductors, fusing, and connectors rather than attempting to draw 50 A through the stock 7-pin circuit.

## Operating architecture

### Shore power

30 A shore inlet -> Power Watchdog -> AIC inverter/charger -> trailer AC distribution and LiFePO4 charging.

This preserves normal campground operation while automatically recharging the house bank.

### Solar

200 to 600 W roof array -> GP-RVC-30-MPPT -> 600 Ah LiFePO4 bank.

Solar remains active whenever available and can coexist with other regulated charging sources.

### Off-grid AC

600 Ah LiFePO4 bank -> AIC-3000-12-SL -> trailer 120 V distribution.

This provides silent operation for normal AC loads and limited A/C or heat-pump runtime without starting the generator.

### Generator backup

RYOBI RYI2022VNM -> Power Watchdog / shore input -> AIC inverter/charger -> trailer loads and battery charging.

The generator is primarily a recovery and backup source rather than something that must run continuously. Because its continuous output is 1,650 W, inverter/charger input and battery-charging behavior must be configured so trailer loads plus charging do not overload the generator.

### Tow vehicle

Tow vehicle -> existing 7-pin auxiliary feed -> trailer battery system.

If testing shows the factory path is insufficient or poorly controlled for LiFePO4 charging, insert a current-limited DC-DC charger. A dedicated high-current charging circuit can be added later without changing the solar or shore-power architecture.

## Installation notes

- Verify the cubby floor and underlying structure before locating the approximately 108 lb battery there.
- Mechanically restrain the battery against movement in every axis.
- Keep the battery-to-inverter 4/0 run as short as practical.
- Mount the MPPT controller vertically, indoors, with required ventilation.
- Verify actual roof layout before purchasing all three panels. The rigid panels are approximately 59.1 x 26.3 inches each.
- Use the battery manufacturer's LiFePO4 charge limits when configuring every charging source.
- Confirm the exact GE A/C model before purchasing the soft starter.
- Confirm generator neutral/ground behavior with the actual Power Watchdog and inverter/charger installation before adding any bonding accessory.
- Measure actual 7-pin voltage and current before selecting the tow-charging configuration.

## Planned end state

The target system is approximately:

- 7.68 kWh nominal LiFePO4 storage
- 3,000 W pure-sine AC inverter capacity
- 600 W roof solar
- 30 A MPPT solar charging
- Automatic shore-power battery charging
- RYOBI generator backup
- Existing Hughes Power Watchdog protection
- Controlled tow-vehicle charging if required
- Soft-start-assisted 13,500 BTU A/C operation

The design intentionally keeps the generator, solar array, battery bank, and shore connection complementary rather than treating any one source as sufficient under every condition. The battery supplies short-term high-demand and silent operation, solar handles ongoing baseline recovery, shore power provides normal full-service operation, and the existing RYOBI provides an independent recovery path when solar is unavailable.