..
  Technote content.

  See https://developer.lsst.io/restructuredtext/style.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.

.. note::

   **This technote is not yet published.**

   This project integrate the wireless infrastructure from Vera Rubin Observatory with NOIRLabs.

.. Add content here.

Introduction:
=============
To satisfy NOIRLab's wifi network integration on new Rubin base facilities building, we required to accomplish L2 and L3 communication between Rubin's network and NOIRLab's CTIO and NOIRLab's Gemini networks. Both NOIRLab's projects presented different challenges depending on their internal policies and wireless network setups. The following documentation is meant to explain how integration was accomplished and by which methods.

Scope:
------
- Explain how NOIRLab's CTIO and Gemini South wireless networks were integrated on Rubin's cisco wireless controller

- Show how communication flows when NOIRLab's CTIO and Gemini South staff access to their respectively wireless networks

- This document focused only on NOIRLab integration and not on Rubin's complete wireless setup

- This document focused only on Rubin's base facilities located in La Serena, Chile.

Document Overview:
------------------
The purpose of this document is to show how L2 and L3 communication was accomplished to provide wireless access services to both projects with their particular policies and setups. No mission-critical or sensitive information is meant to be exposed to this document meaning that no IP addresses or passwords will be disclosed.

For more detailed information on how integration was accomplished, please visit Rubin's Jira epic ticket https://jira.lsstcorp.org/browse/ITP-22 where you will find a list of activities with detailed information, testing, and configurations.

Related Documentation:
^^^^^^^^^^^^^^^^^^^^^^

- Wi-Fi Infrastructure High-Level Design HLD - IT South Confluence Page
- LSE-78 LSST Observatory Network Design
- LSE-309 Summit to Base ITC Design

Design:
=======

Per decision base on project requirements, the Tiger Team (2015/2016 period) chooses Cisco Systems as the vendor for all the LAN network infrastructure, including Wi-Fi, from which its Wireless Lan Controller (WLC) and Lightweight Access Points (AP) ecosystem was the specific technical solution chosen for the project.

On a high-level, the Cisco WLC solution provides central management for all the APs in the network, including wireless settings such as channels, power budget, interference threshold, user authentication, etc... This is implemented by loading a lightweight version of the AireOS software on the APs, which connects back to the WLC using the Control and Provisioning for Wireless Access Points (CAPWAP) protocol.


FlexConnect Mode:
-----------------

FlexConnect mode (previously known as Hybrid Remote Edge Access Point or H-REAP) is a wireless solution for branch office and remote office deployments. It enables customers to configure and control access points in a branch or remote office from the corporate office through a wide area network (WAN) link without deploying a controller in each office. The FlexConnect access points can switch client data traffic locally and perform client authentication locally when their connection to the controller is lost. When they are connected to the controller, they can also send traffic back to the controller. In the connected mode, the FlexConnect access point can also perform local authentication.

When a client associates to a FlexConnect access point, the access point sends all authentication messages to the controller and either switches the client data packets locally (locally switched) or sends them to the controller (centrally switched), depending on the WLAN configuration. Concerning client authentication (open, shared, EAP, web authentication, and NAC) and data packets, the WLAN can be in any one of the following states depending on the configuration and state of controller connectivity:

- **central authentication, central switching:** In this state, the controller handles client authentication, and all client data is tunneled back to the controller. This state is valid only in connected mode.
- **central authentication, local switching:** In this state, the controller handles client authentication, and the FlexConnect access point switches data packets locally. After the client authenticates successfully, the controller sends a configuration command with a new payload to instruct the FlexConnect access point to start switching data packets locally. This message is sent per client. This state is applicable only in connected mode.
- **local authentication, local switching:** In this state, the FlexConnect access point handles client authentication and switches client data packets locally. This state is valid in standalone mode and connected mode.

**Important:** All Rubin base facilities' access points were configured in FlexConnect mode.

NOIRLab CTIO Wireless Networks:
-------------------------------

NOIRLab CTIO provides two different wireless networks. Corporative wireless network access, which allows access to internal services and resources; and a guest wireless network access, which provides Internet access only. Both WLANs are configured for FlexConnect mode, local switching, and central authentication.

CTIO VLANs are injected from CTIO core switch into Rubin's access switches. Rubin access points locally switch data using CTIO links. CTIO wireless VLANs were pruned from Rubin's uplinks, containing the traffic at the access layer, avoiding wireless access to go into upper layers (Distribution / Core layers)

For wireless network access requests, refer to http://www.ctio.noao.edu/noao/node/add/network-connection for self-service registration. You can also send an email to ciss-helpdesk@ctio.noao.edu or create a Jira ticket on the CISS Jira platform. Guest credentials are managed by NOIRLab's IT Ops team and published on Rubin base facilities areas.

CTIO Corp Wireless Network:
^^^^^^^^^^^^^^^^^^^^^^^^^^^
- SSID: newN0a0s
- VLAN: 7 (on Core CTIO SBF)   


CTIO Guest Wireless Network:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- SSID: PublicWifi
- VLAN: 98
  
NOIRLab Gemini South Wireless Networks:
---------------------------------------

NOIRLab Gemini South provides two different wireless networks. Corporative wireless network access, which allows access to internal services and resources; and a guest wireless network access, which provides Internet access only. Both WLANs are configured for FlexConnect mode, local switching, and central authentication.

NOIRLab Gemini South uses 802.1x authentication and accounting service to access its corporate network through the Cisco ISE security policy management platform, to provide secure access to internal network resources.

Gemini VLANs are injected from Gemini's core switch into CTIO's core switch, and then CTIO core switch injects them into Rubin's access switches. Rubin access points locally switch data using CTIO links who also transports Gemini VLANs all the way through Gemini's core switch. Gemini wireless VLANs were pruned from Rubin's uplinks, containing the traffic at the access layer, avoiding wireless access to go into upper layers (Distribution / Core layers). Only authentication/accounting traffic, controlled by Rubin's WLC, will go through Rubin's internal networks and firewalls to reach Gemini Cisco ISE accounting and authentication server.

For wireless network access requests, please contact NOIRLab's IT OPS team.

Gemini South Corporative Wireless Network:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- SSID: GemWifi
- VLAN: 77


Gemini South Guest Wireless Network:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- SSID: GemVisitor
- VLAN: 13


NOIRLab Wireless Integration Diagram:
=====================================

.. figure:: /_static/integration-diagram.jpg
    :name: integration-diagram
        :width: 400 px


Appendix:
=========

Acronyms:
---------

=============      ===============================================================
Term/Acronym       Meaning
=============      ===============================================================
802.1x             IEEE Standard for port-based Network Access Control
AP                 Access Point
AURA               Associated Universities for Research in Astronomy
CAPWAP             Control and Provisioning for Wireless Access Points
CISS               Computing Information Services South
CTIO               Cerro Tololo Inter-American Observatory
EAP                Extensible Authentication Protocol
FTD                Cisco Firepower NG-Firewalls
Gemini             Gemini South Observatory
IEEE               Institute of Electrical and Electronics Engineers
IP                 Internet Protocol
ISE                Cisco Identity Services Engine
IT                 Information Technology
IT OPS             NOIRLab Information Technology Operation Group
L2                 Layer 2
L3                 Layer 3
LAN                Local Area Network
NAC                Network Access Control
NOIRLab            NSF’s National Optical-Infrared Astronomy Research Laboratory
Rubin Obs          Vera C. Rubin Observatory
SBF                South Base Facilities
SSID               Service Set Identifier
VLAN               Virtual Local Area Network
WAN                Wide Area Network
WLAN               Wireless Local Area Network
WLC                Cisco Wireless Controller
=============      ===============================================================
.. Do not include the document title (it's automatically added from metadata.yaml).

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
