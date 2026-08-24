IoT-Based Smart Energy Intelligence & Monitoring System

Highway median pole that harvests 50 W solar and Savonius wind (vehicle wake), stores energy in a 12 V 18 Ah LiFePO4 pack, drives LED lamps and an air purifier, and shows live generation vs consumption on an OLED and a phone/PC Wi‑Fi dashboard.

Hardware build · Wiring · Wokwi sim · Code explained · GitHub upload



















Understand the system



[docs/00-SYSTEM-OVERVIEW.md](docs/00-SYSTEM-OVERVIEW.md)





Simulate with no hardware



[wokwi/](wokwi/) + [docs/03-WOKWI-SIMULATION.md](docs/03-WOKWI-SIMULATION.md)





Flash the real ESP32



[firmware/hardware/smart_energy_monitor/](firmware/hardware/smart_energy_monitor/)





Buy and wire parts



[bom/](bom/) + [docs/02-WIRING-STEP-BY-STEP.md](docs/02-WIRING-STEP-BY-STEP.md)





Log energy to Excel



[software/dashboard/energy_logger.py](software/dashboard/energy_logger.py)





Push to GitHub (main + sub-branches)



[docs/05-GITHUB-UPLOAD.md](docs/05-GITHUB-UPLOAD.md)



Architecture

SOLAR 50W ----+
              |--> 30A solar controller --> 12V LiFePO4 battery
WIND Savonius-+        (diode + XL6009)           |
                                                 |
                    +----------------------------+
                    |
                    |-- fuse --> 12V LED lamps (relay CH1)
                    |-- fuse --> 12V load / inverter (relay CH2)
                    |-- LM2596 12V->5.0V --> ESP32, OLED, sensors, relay coil

The ESP32 never connects to the raw 12 V battery rail. Intelligence runs at 5 V / 3.3 V: measure, decide loads, estimate state of charge, publish HTTP.

Energy intelligence (firmware rules)





Map sensors to watts (Wokwi: pots; hardware: ACS712 × battery volts).



Night (LDR) → street lamps ON.



Poor air (gas analog) → purifier ON.



SOC below 15% → both loads OFF (battery protection).



SOC integrates (P_generation − P_load) / 230.4 Wh.



Phone UI at http://192.168.4.1 and JSON at /data.



Quick start — simulation (no kit)





Open wokwi.com → new ESP32 project.



Paste [wokwi/diagram.json](wokwi/diagram.json) and [wokwi/sketch.ino](wokwi/sketch.ino).



Libraries: contents of [wokwi/libraries.txt](wokwi/libraries.txt).



Press Play. Serial Monitor: Smart median pole ready.



Join AP SmartPole-AP / 12345678 → http://192.168.4.1.



Turn SOLAR / WIND pots; lower LDR lux for night; raise gas for the purifier.

Full click-path: [docs/03-WOKWI-SIMULATION.md](docs/03-WOKWI-SIMULATION.md).

Quick start — hardware





Follow [docs/01-HARDWARE-BUILD.md](docs/01-HARDWARE-BUILD.md) (fuses in last).



Set LM2596 to 5.00 V before ESP32 VIN.



Arduino IDE: open firmware/hardware/smart_energy_monitor/smart_energy_monitor.ino.



Board ESP32 Dev Module, upload, Serial 115200.



Same Wi‑Fi AP and dashboard as Wokwi.



Quick start — PC logger

Connect the laptop to SmartPole-AP, then:

python software/dashboard/energy_logger.py

CSV appears under data/energy_log.csv.

Source map

.
├── README.md                 ← you are here (GitHub landing page)
├── LICENSE                   ← MIT
├── CONTRIBUTING.md           ← main vs feature branches
├── bom/                      ← Excel + CSV parts list
├── diagrams/                 ← SVG power and ESP32 wiring
├── docs/                     ← full documentation set
├── firmware/hardware/        ← production Arduino sketch + config.h
├── wokwi/                    ← simulation sketch + diagram.json
├── software/dashboard/       ← Python JSON→CSV logger
└── .github/                  ← issue and pull-request templates



Git branches (after you upload)







Branch



Role





main



Default GitHub branch — stable project + docs





develop



Integration before a release





feature/firmware



ESP32 programs





feature/hardware



BOM, wiring, diagrams





feature/simulation



Wokwi only





feature/dashboard



Logger / web





docs/github-guide



Upload notes

Step-by-step Windows commands: [docs/05-GITHUB-UPLOAD.md](docs/05-GITHUB-UPLOAD.md).

Honest prototype limits





A 50 W panel + small Savonius + 18 Ah pack will not run a household AC purifier all night plus two bright lamps. Use short purifier cycles or a 12 V DC fan/filter.



Do not use the XH‑M601 lead‑acid charger with LiFePO4. The pack BMS + 30 A controller (LFP mode) is the charger.



INA219 is accurate only to about ±3.2 A. Use ACS712 30 A on the battery/load cable.



Wokwi cannot simulate the solar controller, turbine, or inverter — only the control and dashboard logic.



License

MIT — see [LICENSE](LICENSE).
