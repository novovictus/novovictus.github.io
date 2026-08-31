# Catalina 134BHX Off-Grid Power

**Status:** Planning and phased implementation

This sidequest documents a phased electrical upgrade for the Coachmen Catalina Summit Series 7 134BHX. The design goal is to preserve normal shore-power and tow-vehicle operation while adding battery, inverter, solar, and generator capability for meaningful off-grid use. The implementation is intentionally incremental so each phase produces a usable system and measured results can guide later expansion.

The trailer is factory pre-wired for Go Power solar. The identified prep location is labeled for a maximum 30 amps of solar and is intended for the Go Power/Dometic controller installation. The preferred integration strategy is to retain the Go Power/Dometic ecosystem for the inverter/charger, display and communications, MPPT controller, and matching solar panels. This reduces custom integration work, keeps expansion within documented configurations, and provides a cleaner support and warranty path. Third-party LiFePO4 batteries remain appropriate because Dometic battery offerings do not align as well with the desired capacity, modularity, and value.

## Design baseline

The planned system uses three independent charging sources feeding a common 12 V LiFePO4 house bank:

- Roof solar through a 30 A MPPT controller
- 120 V shore power or portable generator through an inverter/charger
- Tow-vehicle charging, retained initially and optionally upgraded to controlled DC-DC charging

The battery bank supplies the existing 12 V trailer loads directly and the 120 V system through a 3,000 W pure-sine inverter/charger.

The battery architecture is modular. The initial system uses one Vatrer 12.8 V 300 Ah self-heating Bluetooth LiFePO4 battery with a 200 A BMS and can expand to a second matching battery in parallel. A two-battery bank provides approximately 600 Ah / 7.68 kWh nominal storage while preserving redundancy and reducing the number of parallel branches, fuses, disconnects, cables, and balance points compared with a three-battery design. The permanent DC distribution should be designed from the first installation for the eventual two-battery bank.

The 300 Ah platform is also being treated as a candidate battery standard for Project N.O.M.A.D. The Catalina serves as the lower-risk validation platform for capacity, BMS behavior, self-heating, Bluetooth telemetry, charging compatibility, and parallel operation before any broader standardization decision.

Existing equipment already available:

- Coachmen Catalina Summit Series 7 134BHX
- GE Profile PLH13XAHWH1 13,500 BTU rooftop A/C with heat pump
- RYOBI RYI2022VNM inverter generator, 2,050 W starting / 1,650 W running
- Hughes Power Watchdog WPC30A
- Factory 30 A shore-power service
- Factory tow-vehicle charging circuit
- Factory Go Power Wired for Solar prep
- Home Assistant monitoring and temperature-control capability

## Solar controller limits

The Go Power GP-RVC-30-MPPT supplied with the Eclipse system is the design constraint for the planned solar array:

- System voltage: 12/24 V
- Rated charge current: 30 A
- Maximum PV input voltage: less than 100 V
- Maximum PV input power: 600 W at 12 V, 1,200 W at 24 V
- Battery profiles include LiFePO4
- RS485 and CAN bus communication

The 200 W rigid Eclipse panel is rated at 20.4 Vmp, 9.6 A Imp, and 24.09 Voc. The maximum planned configuration is three matching 200 W panels while remaining within the controller's documented 600 W limit at 12 V.

Using matching Go Power panels throughout the phased expansion preserves documented electrical compatibility, connectors, mounting approach, physical dimensions, and controller support rather than requiring the array to be redesigned as capacity is added.

## Acquisition and installation strategy

Purchase order and installation order do not need to be identical. Unusually favorable pricing can justify acquiring a major component before its installation phase, but the permanent installation should still be completed as an integrated system rather than creating temporary wiring that will be replaced later.

The AIC inverter/charger is available both as a bare PowerTrak-ready unit and as part of the AIC-3000-12-SL-DIS-KIT. The complete display package is preferred because the intended installation includes the PowerTrak screen/controller and matched communications. A deeply discounted bare AIC can still be considered, but its actual system cost should include the separately sourced display, communications, cabling, fixtures, protection, and integration effort rather than comparing only inverter purchase prices.

The same principle applies to the broader build. At this project's scale, reduced integration time, documented interoperability, supportability, and a cleaner warranty path can justify a higher component price. Custom integration remains possible where it materially improves the design, particularly at the battery bank.

The 300 Ah Vatrer is preferred over the previously considered 200 Ah class because the gateway cost is only modestly higher while starting capacity increases by 50 percent. It reaches half of the intended final 600 Ah bank immediately, remains physically serviceable, and allows the final target to be reached with one later matching battery instead of two. Fewer batteries also mean fewer high-current connection points and fewer components that can become imbalance or service issues.

Prices below are planning references and should be rechecked before purchase.

## Purchase and installation phases

### Phase 0: Existing equipment

No purchase required.

| Component | Model | Reference price | Link | Purpose |
| --- | --- | ---: | --- | --- |
| Inverter generator | RYOBI RYI2022VNM | Already owned | https://www.directtoolsoutlet.com/products/RYI2022VNM | Generator backup and bulk charging source |
| 30 A electrical protection | Hughes Power Watchdog WPC30A | Already owned | https://hughesautoformers.com/product/pwd30-epo/ | Shore/generator input monitoring and protection |
| Factory solar prep | Go Power / Dometic Wired for Solar | Installed | https://gopowersolar.com/wired-for-solar/ | Existing roof-to-controller solar wiring and controller location |

### Phase 1: Battery, inverter/charger, and permanent electrical backbone

Install one Vatrer 12.8 V 300 Ah self-heating Bluetooth LiFePO4 battery with 200 A BMS and the complete 3,000 W inverter/charger system. This creates a functional shore-charged and battery-powered trailer before solar is added and allows the core electrical architecture to be validated independently.

Preferred inverter integration:

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 3,000 W advanced inverter/charger with display | Go Power AIC-3000-12-SL-DIS-KIT | 1 | Verify at purchase | https://gopowersolar.com/products/aic-series-3000-watt-sl-advanced-inverter-charger/ |
| Bare inverter alternative | Go Power AIC-3000-12-SL / 83885 | 1 | Verify at purchase | https://autoplicity.com/21124805-go-power-83885-aic300012sl-3000w-advanced-inverter150a-charger-12v-30a-ats-pt-ready |
| Matched inverter DC installation hardware | Go Power GP-DC-KIT5 or documented equivalent | 1 | Verify at purchase | https://gopowersolar.com/products/inverter-installation-kits/ |
| Self-heating LiFePO4 battery | Vatrer 12.8 V 300 Ah Bluetooth, self-heating, 200 A BMS | 1 | $629.99 reference | https://www.vatrerpower.com/products/vatrer-12v-300ah-bluetooth-lifepo4-lithium-battery-with-self-heating-200a-bms-supports-low-temp-charging-4-f-5000-cycles-2560w-power |

The AIC-3000-12-SL provides 3,000 W pure-sine inverter capacity, high-rate shore/generator charging, and transfer functionality appropriate for the trailer's existing 30 A service. The preferred DIS package adds the PowerTrak display/controller and matched communications rather than treating those as later custom integration items.

The permanent DC backbone should be sized and arranged from the beginning for an eventual two-battery bank. This includes appropriately rated battery protection, disconnects, positive and negative distribution, inverter protection, cabling, and mechanical battery restraint. Exact cable lengths and fixtures should be selected after measuring the final battery, bus, fuse, disconnect, and inverter locations.

One 300 Ah battery is a legitimate operating configuration for Phase 1 and provides approximately 3.84 kWh nominal storage. Its 200 A BMS does not imply unrestricted use of the AIC's full 3,000 W output. At full inverter output, a 12 V system can approach approximately 250 A or more of DC current after conversion losses and battery-voltage variation. Actual inverter loading must therefore remain within the selected battery BMS continuous and surge discharge limits until the second battery is installed.

With two matching 300 Ah batteries connected through a balanced parallel distribution, aggregate current capability is ample for the AIC's 3,000 W class while individual battery current is substantially reduced. The second battery also provides service redundancy: if one battery must be isolated, the camper retains approximately 300 Ah / 3.84 kWh of nominal storage.

The AIC's charging current must likewise be configured to the selected battery manufacturer's maximum charge-current specification. Adding the second parallel battery later increases both capacity and aggregate charge/discharge capability.

Phase 1 validation should include:

- Shore-power passthrough and transfer behavior
- LiFePO4 charging configuration and measured charging current
- Normal 12 V trailer operation
- Inverter operation for outlets, laptops, monitor, refrigerator-related loads, and other normal AC loads
- Controlled microwave testing within the battery BMS limits
- Inverter standby consumption
- PowerTrak and app monitoring
- Vatrer Bluetooth telemetry, cell balance, BMS behavior, self-heating behavior, and state-of-charge tracking
- Generator charging behavior with AIC input/charge limits configured for the RYOBI's 1,650 W continuous output

### Phase 2: Base solar and off-grid charging

Install the supported Go Power Eclipse system using the factory solar prep. This adds renewable off-grid recovery after the battery/inverter system has already been commissioned.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| 200 W rigid Eclipse solar kit | Go Power GP-ECLIPSE-200 / 83315 | 1 | Verify at purchase | https://gopowersolar.com/products/rigid-eclipse-solar-kit/ |

The base kit establishes the GP-RVC-30-MPPT controller, first matching 200 W panel, solar cabling, and mounting infrastructure. The existing factory roof/main solar wiring run should be retained after its conductor size, polarity, routing, and condition are verified during installation.

At the end of Phase 2 the camper can charge from shore/generator, solar, and the retained tow-vehicle circuit.

### Phase 3: A/C startup optimization

Add a soft starter matched to the installed GE Profile PLH13XAHWH1 13,500 BTU rooftop A/C with heat pump before treating either the 3,000 W inverter or the 1,650 W continuous-output RYOBI as an A/C source.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| RV A/C soft starter | Micro-Air EasyStart 364, final 115 V application/SKU to verify at purchase | 1 | Verify at purchase | https://www.micro-air.com/products_easystart_364_softstarter_microair.cfm |

The PLH13XAHWH1 is a conventional-compressor GE Profile heat-pump rooftop unit. GE documentation for the PLH13X family specifies heat-pump operation down to approximately 25 F. The Micro-Air EasyStart 364 is the appropriate soft-start family for this class of RV rooftop compressor, with the exact 115 V application and installation configuration to be verified against the unit data plate and current Micro-Air documentation before purchase.

Because the initial build occurs at the end of summer, battery-powered A/C is not required to validate Phases 1 and 2. The soft start establishes the capability for later warm-weather testing after the battery bank has also had time to expand.

### Phase 4+: Incremental battery and solar expansion

Add one matching 300 Ah Vatrer battery and matching 200 W Eclipse expansion panels over the following months based on measured consumption, solar recovery, available roof area, and winter-trip requirements.

Potential expansion components:

| Component | Model / part number | Maximum additional qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| Matching self-heating 300 Ah LiFePO4 battery | Same Vatrer 300 Ah model as Phase 1 | 1 | Verify at purchase | https://www.vatrerpower.com/products/vatrer-12v-300ah-bluetooth-lifepo4-lithium-battery-with-self-heating-200a-bms-supports-low-temp-charging-4-f-5000-cycles-2560w-power |
| 200 W rigid Eclipse expansion | Go Power GP-ECLIPSE-200-E / 83316 | 2 | Verify at purchase | https://gopowersolar.com/products/rigid-eclipse-solar-kit/ |

The planning ceiling remains approximately 600 Ah / 7.68 kWh of battery storage and 600 W of roof solar, but those numbers are targets rather than mandatory purchases. The second battery doubles runtime, provides full-bank current headroom for the inverter, and preserves 300 Ah of usable storage if one unit must be isolated. Each additional panel increases daytime production and recovery rate. Actual measurements from the earlier phases should determine whether the final expansion is necessary before the winter trip.

For the two-battery parallel bank, use matching battery models and follow the manufacturer's parallel-battery requirements. Batteries should be individually protected and isolatable where appropriate, connected through a balanced low-resistance distribution arrangement, and brought to compatible state of charge before being paralleled. This allows a failed or serviceable battery to be isolated while retaining a useful 300 Ah bank.

### Optional tow-vehicle charging upgrade

The existing 7-pin charge feed can remain part of the trailer system, but high-current lithium charging should not be assumed from the factory wiring. If towing charge performance is inadequate, add a controlled DC-DC charger and size its input current to the actual vehicle/trailer wiring.

| Component | Model / part number | Qty. | Reference price | Link |
| --- | --- | ---: | ---: | --- |
| Adjustable DC-DC charger | Victron Orion XS 12/12-50A, ORI121217050 | 1 | $349 MSRP reference | https://www.victronenergy.com/dc-dc-converters/orion-xs-dc-dc-battery-chargers |

The Orion XS can be current-limited below its 50 A maximum. A dedicated higher-current vehicle feed should use appropriately sized conductors, fusing, and connectors rather than attempting to draw 50 A through the stock 7-pin circuit.

## Seasonal heating strategy

The winter operating plan does not depend on battery-powered heat-pump operation.

The installed GE Profile PLH13XAHWH1 heat pump is specified for operation down to approximately 25 F, but the off-grid strategy transitions to propane earlier when ambient temperature and heat-pump efficiency make battery operation unattractive. Below roughly 30 F, propane is therefore the practical primary off-grid heat source. When shore power is available, resistive electric heat can be used instead of consuming propane. The heat pump remains useful when ambient conditions are appropriate but is not treated as the critical winter heat source.

Prior real-world winter operation provides a useful baseline. The camper remained usable with a single 750 W resistive heater during cold Lancaster conditions. At its lower setting, measured consumption was below approximately 400 W and the camper remained well above freezing with outside temperatures in the sub-20s F. The camper was also supporting normal office/electrical use during this period.

Home Assistant was configured to enable the heater only after interior temperature fell into the 40s. That strategy can continue off grid as a backup heat layer. The propane furnace remains primary, while the HA-controlled resistive heater can establish a lower temperature floor if propane is exhausted or the furnace fails overnight.

The backup automation should include a battery state-of-charge cutoff appropriate to the installed bank and an alert when resistance heat is cycling unexpectedly. This prevents the fallback heater from silently exhausting the battery while also making repeated backup operation an indication that the primary heat source needs attention.

## Operating architecture

### Shore power

30 A shore inlet -> Power Watchdog -> AIC inverter/charger -> trailer AC distribution and LiFePO4 charging.

This preserves normal campground operation while automatically recharging the house bank. Resistive heat can be used normally when shore capacity permits.

### Solar

200 to 600 W matching roof array -> GP-RVC-30-MPPT -> modular 300 to 600 Ah LiFePO4 bank.

Solar remains active whenever available and can coexist with other regulated charging sources.

### Off-grid AC

LiFePO4 bank -> AIC-3000-12-SL -> trailer 120 V distribution.

Phase 1 begins with 300 Ah / 3.84 kWh of nominal storage and expands to 600 Ah / 7.68 kWh if measured requirements justify the second battery. Normal AC loads are available immediately, while maximum inverter loading remains constrained by the installed battery bank's aggregate BMS capability.

### Generator backup

RYOBI RYI2022VNM -> Power Watchdog / shore input -> AIC inverter/charger -> trailer loads and battery charging.

The generator is primarily a recovery and backup source rather than something that must run continuously. Because its continuous output is 1,650 W, inverter/charger input and battery-charging behavior must be configured so trailer loads plus charging do not overload the generator.

### Tow vehicle

Tow vehicle -> existing 7-pin auxiliary feed -> trailer battery system.

If testing shows the factory path is insufficient or poorly controlled for LiFePO4 charging, insert a current-limited DC-DC charger. A dedicated high-current charging circuit can be added later without changing the solar or shore-power architecture.

## Installation notes

- Determine battery, inverter, fuse, disconnect, and bus locations together before ordering final high-current cable lengths.
- Verify the cubby floor and underlying structure for the selected modular battery installation.
- Mechanically restrain every battery against movement in every axis.
- Keep battery/bus-to-inverter 4/0 runs as short as practical.
- Size the permanent DC distribution for the eventual two-battery 600 Ah bank even when only one battery is initially installed.
- Respect each battery's BMS continuous discharge, surge discharge, maximum charge current, low-temperature, and parallel-operation limits.
- Configure AIC charging current for both the installed battery capacity and the available AC source.
- Mount the MPPT controller indoors according to manufacturer ventilation and orientation requirements.
- Verify factory solar wiring gauge, polarity, routing, protection, and connector condition before energizing the array.
- Verify actual roof layout before purchasing all three panels. The rigid panels are approximately 59.1 x 26.3 inches each.
- Installed rooftop HVAC is GE Profile PLH13XAHWH1, 13,500 BTU A/C with heat pump. Verify its data plate and current Micro-Air EasyStart 364 application instructions before soft-start installation.
- Confirm generator neutral/ground behavior with the actual Power Watchdog and inverter/charger installation before adding any bonding accessory.
- Measure actual 7-pin voltage and current before selecting the tow-charging configuration.

## Planned end state

The target system can grow to approximately:

- Two matching Vatrer 12.8 V 300 Ah self-heating Bluetooth LiFePO4 batteries with 200 A BMSs
- 600 Ah / 7.68 kWh nominal storage
- 3,000 W pure-sine AC inverter capacity
- PowerTrak display/controller and Go Power/Dometic communications
- Up to 600 W of matching Go Power Eclipse roof solar
- 30 A MPPT solar charging
- Automatic shore-power battery charging
- RYOBI generator backup
- Existing Hughes Power Watchdog protection
- Controlled tow-vehicle charging if required
- Micro-Air EasyStart-assisted GE Profile PLH13XAHWH1 13,500 BTU A/C and heat-pump operation
- Propane primary off-grid winter heat with HA-controlled resistance heat as a battery-backed temperature-floor fallback

The 600 Ah / 600 W configuration is a planning ceiling rather than a requirement. The design deliberately reaches a useful 300 Ah / 3.84 kWh operating state in Phase 1 and uses measured performance to decide whether the second battery and full solar expansion are needed before the winter trip.

The generator, solar array, battery bank, shore connection, and propane heating system are complementary rather than treating any one source as sufficient under every condition. Battery storage supplies silent operation and short-duration demand, solar handles ongoing recovery, shore power provides normal full-service operation and electric heat, propane provides efficient off-grid winter heat, and the existing RYOBI provides an independent electrical recovery path when solar is unavailable.