# OpenGait

An open-source closed-loop functional electrical stimulation (FES) walking system for people with spinal cord injury.

> 🚧 **Work in Progress** — This project is in the architecture and documentation phase. No hardware has been assembled yet.

## What is OpenGait?

OpenGait is an open-source closed-loop Functional Electrical Stimulation (FES) walking system designed to restore ambulatory capability for individuals with complete spinal cord injury. The project targets a hardware cost under $2,000—an order of magnitude less than commercial alternatives—while implementing closed-loop control that doesn't exist in any commercially available surface FES system.

### The Problem

The only FDA-approved surface FES walking system (Parastep I) costs ~$13,000, uses open-loop control with no sensor feedback, and achieves walking distances of only 6-9m for most users. Research systems have demonstrated dramatically better results with closed-loop control, but remain locked in labs at $50,000+.

### Our Approach

| Metric | Target |
|--------|--------|
| Hardware cost | ~$1,064 |
| Walking speed | 0.1-0.2 m/s |
| Control type | Closed-loop with predictive timing |
| Trigger latency | <60ms |
| Session duration | 15-30 min (fatigue-limited) |

### System Architecture

OpenGait uses a distributed edge architecture:

1. **Sensing Layer**: 7 Arduino Nano 33 BLE Sense Rev2 nodes with on-device TinyML gait phase classification
2. **Control Layer**: Raspberry Pi 5 + Hailo-8L AI HAT for sensor fusion and 200ms trajectory prediction
3. **Stimulation Layer**: 8-channel surface FES via modulated TENS units with hardware safety interlocks

Key design decisions:
- Direct GPIO triggering from shank sensors for minimum-latency stimulation path
- Independent RP2040 safety watchdog with hardware kill chain
- Hybrid rule-based + ML gait phase detection for reliability

## Documentation

This repository is organized as an Obsidian notebook. See [00-Index.md](00-Index.md) for the complete table of contents, including:

- System architecture and design rationale
- Complete bill of materials (~$1,064)
- Safety standards and compliance (IEC 60601-2-10)
- Gait biomechanics and sensing strategies
- Open research questions

## Current Status

**Phase:** Architecture & Documentation

| Completed | In Progress | Planned |
|-----------|-------------|---------|
| System architecture | Hardware sourcing | Sensor node assembly |
| Component selection | Power system design | Safety system validation |
| BOM validation | | Unity3D simulation |
| Safety design | | Bench testing |

No hardware has been assembled. No walking has occurred.

---

## License

This project uses a **non-commercial open source** licensing model.

### Software

All software in this repository is licensed under the [PolyForm Noncommercial License 1.0.0](https://polyformproject.org/licenses/noncommercial/1.0.0/).

You are free to:
- **Use** — Build your own OpenGait system for personal use
- **Study** — Use the code for research, education, or experimentation
- **Modify** — Create derivatives and improvements
- **Share** — Distribute copies to others for non-commercial purposes

You may not:
- **Commercialize** — Sell devices, kits, or services based on this project without a separate license

### Hardware Designs

Hardware designs (schematics, PCB layouts, 3D printable files) are licensed under [Creative Commons BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).

### Commercial Use

Commercial use requires a separate license. This includes:
- Selling OpenGait devices, kits, or components
- Incorporating this technology into commercial products
- Using this system in fee-generating clinical services

To discuss commercial licensing: [your contact email]

### Non-Profit Healthcare Organizations

If you're a non-profit hospital, academic medical center, or rehabilitation research institution interested in using OpenGait for clinical research or patient care, please reach out. I'm open to granting appropriate permissions for legitimate healthcare applications that could help patients.

### Why This License?

I want OpenGait to be freely available to individuals with SCI, researchers, and the maker community. I also want to prevent commercial exploitation without giving back to the community that built it.

If a company wants to commercialize this work, they can contact me—I'm not opposed to commercial partnerships that advance the mission, but I want to be part of that conversation.

---

## Contributing

Contributions are welcome! By submitting a pull request, you agree that your contributions will be licensed under the same terms as the rest of the project.

Areas where help is especially needed:
- **Clinical review** — PTs and rehab engineers to review safety protocols
- **Embedded systems** — Arduino/RPi integration and optimization
- **TinyML** — Gait phase classification model development
- **Unity3D** — Simulation environment for synthetic training data
- **Documentation** — Build guides, tutorials, accessibility review

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## Disclaimer

OpenGait is an experimental research project. It is **not** a medical device and has not been evaluated or approved by any regulatory agency.

Building and using this system involves inherent risks including electrical burns, muscle fatigue, falls, and fractures. Do not attempt to build or use this system without:

- Prior experience with FES under clinical supervision
- Consultation with your physician
- Appropriate safety equipment (walker, harness, spotter)
- Full understanding of the risks involved

The authors and contributors accept no liability for any injury or damage resulting from the construction or use of this system.

---

## Contact

Email: [ryan.davis@vectorjoy.dev](mailto:ryan.davis@vectorjoy.dev)

---

*OpenGait: Because the technology to walk shouldn't cost more than a car.*
