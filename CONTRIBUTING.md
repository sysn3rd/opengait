# Contributing to OpenGait

Thank you for your interest in contributing to OpenGait. This project aims to make closed-loop FES walking technology accessible to individuals with spinal cord injury, and contributions from the community are essential to that mission.

## Code of Conduct

We expect all contributors to be civil and decent to each other. This means:

- Be respectful and constructive in discussions
- Welcome newcomers and help them get started
- Accept feedback gracefully and give it kindly
- Focus on what's best for the project and the community it serves
- Remember that many contributors may be personally affected by SCI

Harassment, discrimination, or hostile behavior will not be tolerated.

## How to Contribute

### Areas Where Help is Needed

- **Clinical review** — Physical therapists and rehab engineers to review safety protocols
- **Embedded systems** — Arduino/RPi integration and optimization
- **TinyML** — Gait phase classification model development
- **Unity3D** — Simulation environment for synthetic training data
- **Documentation** — Build guides, tutorials, accessibility review

### Getting Started

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature-name`)
3. Make your changes
4. Submit a pull request

### Pull Request Requirements

All pull requests must meet the following requirements before merging:

1. **Code review** — At least one maintainer must review and approve the changes
2. **Unit tests** — New code must include appropriate unit tests; existing tests must pass
3. **Documentation** — Update relevant documentation if your changes affect user-facing behavior or system architecture
4. **Attribution** — Credit any external sources, libraries, or prior work that influenced your contribution

### Commit Messages

Write clear, descriptive commit messages that explain what changed and why. Use the imperative mood ("Add feature" not "Added feature").

### Safety-Critical Code

This project involves electrical stimulation of human muscles. Code that affects the stimulation layer, safety watchdog, or sensor processing requires:

- Extra scrutiny during review
- Comprehensive test coverage
- Clear documentation of failure modes
- Maintainer sign-off

## Licensing

By submitting a pull request, you agree that your contributions will be licensed under the same terms as the rest of the project:

- **Software**: [PolyForm Noncommercial License 1.0.0](https://polyformproject.org/licenses/noncommercial/1.0.0/)
- **Hardware designs**: [Creative Commons BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

You must have the right to submit the code under these terms. Do not submit code that:

- Is copied from sources with incompatible licenses
- Contains proprietary or confidential information
- Infringes on patents or other intellectual property

If your contribution incorporates third-party code, clearly document the source and its license in your pull request.

## Questions?

If you're unsure about anything, open an issue to discuss before starting work. We'd rather help you get started on the right path than have you spend time on something that doesn't fit the project's direction.
