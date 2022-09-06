#**Autopilot Installation Steps**#
Following are the steps to install Autopilot.

1. Download yaml file from the Enterprise Spinnaker repository in the GitHub

	```    
	$ wget https://raw.githubusercontent.com/OpsMx/enterprise-spinnaker/
	oes3.10/charts/oes/values-APforOSS.yaml
	```

2. Update Autopilot UI and Autopilot Gate entries
 
```
# Autopilot UI URL configuration
oesUI: 
  host: << AUTOPILOT UI URL Example autopilot.opsmx.net >>
# Autopilot Gate URL configuration
oesGate: 
  host: << AUTOPILOT GATE URL Example autopilot-gate.opsmx.net >>
```

3. Update Spinnaker Deck URL in dashboard section of values-APforOSS.yaml
    
```
#Dashboard Service 
dashboard: 
  config: 
    spinnakerLink: <<SPINNAKER DECK URL Example spinaker.opsmx.net>>
```
<!-- blank line -->
<figure class="video_container">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/oCsW88kl9_g" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</figure>
<!-- blank line -->
