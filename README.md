# Arrival Load Balancer (ALB) Releases

This repository hosts the public ALB release assets and the user documentation
for the EuroScope plugin.

The current packaged plugin layout is:

```text
ALB.dll
ALBCore.dll
alb-config.json
```

Common optional files:

```text
ALBLoader.ini
alb-config.default.json
```

EuroScope should load `ALB.dll`. The loader then starts `ALBCore.dll`.

User documentation:
https://sodgaard.github.io/Euroscope-ALB_Releases/

Source/development repository:
https://github.com/sodgaard/ES-ArrivalLoadBallance
