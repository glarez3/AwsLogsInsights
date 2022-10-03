# CloudWatch Logs Insights

___


fields @timestamp, @message
| stats count(*) as records by srcAddr as SourceIP, srcPort as OriginPort, dstPort as DestinationPort, dstAddr as DestinationIP, action
| filter interfaceId="eni-00fe3e7819e9fc592" # Paste eni of the resource you would like to analize.
#| filter dstPort="4120" # Filter by specific port on destination
| sort HitCount desc

## AFP VPC Flow logs denied IP

    filter action="REJECT"
    | stats count(*) as numRejections by srcAddr
    | sort numRejections desc
    | filter srcAddr = "91.210.107.56" # Ver si se esta bloqueando alguna IP en la VPC "vpc-4a62ee2f AFP VPC"
_

## Comunicacion (Puertos e IP) por ENI

    fields @timestamp, @message
    | stats count(*) as records by srcAddr as SourceIP, srcPort as OriginPort, dstPort as DestinationPort, dstAddr as DestinationIP, action
    | filter interfaceId="eni-e0329698"
    | filter dstPort="4120" or dstPort="1521" or dstPort="22" or dstPort="7001" or dstPort="1523"
    | sort HitCount desc
_

## Comunicaciones por puertos

    fields @timestamp, @message
    | stats count(*) as records by srcAddr as SourceIP, srcPort as OriginPort, dstPort as DestinationPort, dstAddr as DestinationIP, action
    | filter dstPort="7777"
    | sort HitCount desc

 _

## Ver trafico de IP o segmentos en vpc

    fields @timestamp, srcAddr, srcPort, dstAddr, dstPort
    | sort srcAddr desc
    #| filter isIpv4InSubnet(srcAddr,"10.146.0.0/22") # Buscar redes enteras
    #| filter srcAddr like "10.150.180.86" # Buscar una sola IP sin mascara de red
