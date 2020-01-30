---
layout: post
title: "VBA Excel for String manipulation hash code, get an id from string"
categories: notes excel vba
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [excel vba programming]
excerpt: exemple de fonctions Excel
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>


# Fonction permettant de retirer les accents et mettre en minuscule en option
~~~~
Function SansAccent(DONNEE As String, enMinuscule As Boolean) As String
Dim I1 As Byte, Carac As Integer, I As Integer, Trouve As String

For I = 1 To Len(DONNEE)
    Carac = asc(Mid(DONNEE, I, 1))
    If Carac > 223 Then
    Select Case Carac
        Case 224 To 229 'a
            Trouve = "a"
        Case 232 To 235
            Trouve = "e"
        Case 240 To 246
            Trouve = "o"
        Case 249 To 252
            Trouve = "u"
        Case 236 To 239
            Trouve = "i"
        Case 253, 255
            Trouve = "y"
        Case 231
             Trouve = "c"
    End Select
        If Trouve <> "" Then DONNEE = Replace(DONNEE, Chr(Carac), Trouve)
        Trouve = ""
    End If
Next I
If enMinuscule = True Then
    SansAccent = LCase(DONNEE)
Else
    SansAccent = DONNEE
End If
    
End Function

~~~~

# Description des fonctions de hash
exemple de fonctions de hash

retourne 4 caractères: (la constante cutoff permet de configurer le nb de car)	
=BASE64SHA1("totdddddo")
ns8Z

retourne 4 chiffres
=CRC16HASH("totdddddo")
7262

retourne 5 chiffres
=CRC16NUMERIC("totdddddo")
29282

##BASE64SHA1
~~~~
Public Function BASE64SHA1(ByVal sTextToHash As String)

    Dim asc As Object
    Dim enc As Object
    Dim TextToHash() As Byte
    Dim SharedSecretKey() As Byte
    Dim bytes() As Byte
    Const cutoff As Integer = 4

    Set asc = CreateObject("System.Text.UTF8Encoding")
    Set enc = CreateObject("System.Security.Cryptography.HMACSHA1")

    TextToHash = asc.GetBytes_4(sTextToHash)
    SharedSecretKey = asc.GetBytes_4(sTextToHash)
    enc.Key = SharedSecretKey

    bytes = enc.ComputeHash_2((TextToHash))
    BASE64SHA1 = EncodeBase64(bytes)
    BASE64SHA1 = Left(BASE64SHA1, cutoff)

    Set asc = Nothing
    Set enc = Nothing

End Function

Private Function EncodeBase64(ByRef arrData() As Byte) As String

    Dim objXML As Object
    Dim objNode As Object

    Set objXML = CreateObject("MSXML2.DOMDocument")
    Set objNode = objXML.createElement("b64")

    objNode.DataType = "bin.base64"
    objNode.nodeTypedValue = arrData
    EncodeBase64 = objNode.Text

    Set objNode = Nothing
    Set objXML = Nothing

End Function

~~~~

##CRC16HASH

~~~~


Function CRC16HASH(txt)

Dim x As Long

Dim mask, I, j, nC, crc As Integer

Dim c As String



crc = &HFFFF



For nC = 1 To Len(txt)

    j = asc(Mid(txt, nC, 1))

    crc = crc Xor j

    For j = 1 To 8

        mask = 0

        If crc / 2 <> Int(crc / 2) Then mask = &HA001

        crc = Int(crc / 2) And &H7FFF: crc = crc Xor mask

    Next j

Next nC



CRC16HASH = Hex$(crc)

End Function
~~~~

## CRC16NUMERIC
~~~~

Dim USCRC(0 To 255)  As Long



Public Function CRC16NUMERIC(s As String) As String

Dim x As Long

Dim crc As Long

Dim I As Integer



If (USCRC(1) <> &HC0C1) Then

   mbus_FillCRCTable

End If



crc = 65535

For I = 1 To Len(s)

    x = asc(Mid(s, I, 1))

    x = (crc Xor x) And 255

    x = USCRC(x) And 65535

    crc = (crc \ 256) Xor x

Next I

CRCLo = crc Mod 256

CRCHi = (crc - CRCLo) / 256



CRC16NUMERIC = crc

End Function





Public Sub mbus_FillCRCTable()



USCRC(0) = &H0: USCRC(1) = &HC0C1: USCRC(2) = &HC181: USCRC(3) = &H140

USCRC(4) = &HC301: USCRC(5) = &H3C0: USCRC(6) = &H280: USCRC(7) = &HC241

USCRC(8) = &HC601: USCRC(9) = &H6C0: USCRC(10) = &H780: USCRC(11) = &HC741

USCRC(12) = &H500: USCRC(13) = &HC5C1: USCRC(14) = &HC481: USCRC(15) = &H440

USCRC(16) = &HCC01: USCRC(17) = &HCC0: USCRC(18) = &HD80: USCRC(19) = &HCD41

USCRC(20) = &HF00: USCRC(21) = &HCFC1: USCRC(22) = &HCE81: USCRC(23) = &HE40

USCRC(24) = &HA00: USCRC(25) = &HCAC1: USCRC(26) = &HCB81: USCRC(27) = &HB40

USCRC(28) = &HC901: USCRC(29) = &H9C0: USCRC(30) = &H880: USCRC(31) = &HC841

USCRC(32) = &HD801: USCRC(33) = &H18C0: USCRC(34) = &H1980: USCRC(35) = &HD941

USCRC(36) = &H1B00: USCRC(37) = &HDBC1: USCRC(38) = &HDA81: USCRC(39) = &H1A40

USCRC(40) = &H1E00: USCRC(41) = &HDEC1: USCRC(42) = &HDF81: USCRC(43) = &H1F40

USCRC(44) = &HDD01: USCRC(45) = &H1DC0: USCRC(46) = &H1C80: USCRC(47) = &HDC41

USCRC(48) = &H1400: USCRC(49) = &HD4C1: USCRC(50) = &HD581: USCRC(51) = &H1540

USCRC(52) = &HD701: USCRC(53) = &H17C0: USCRC(54) = &H1680: USCRC(55) = &HD641

USCRC(56) = &HD201: USCRC(57) = &H12C0: USCRC(58) = &H1380: USCRC(59) = &HD341

USCRC(60) = &H1100: USCRC(61) = &HD1C1: USCRC(62) = &HD081: USCRC(63) = &H1040

USCRC(64) = &HF001: USCRC(65) = &H30C0: USCRC(66) = &H3180: USCRC(67) = &HF141

USCRC(68) = &H3300: USCRC(69) = &HF3C1: USCRC(70) = &HF281: USCRC(71) = &H3240

USCRC(72) = &H3600: USCRC(73) = &HF6C1: USCRC(74) = &HF781: USCRC(75) = &H3740

USCRC(76) = &HF501: USCRC(77) = &H35C0: USCRC(78) = &H3480: USCRC(79) = &HF441

USCRC(80) = &H3C00: USCRC(81) = &HFCC1: USCRC(82) = &HFD81: USCRC(83) = &H3D40

USCRC(84) = &HFF01: USCRC(85) = &H3FC0: USCRC(86) = &H3E80: USCRC(87) = &HFE41

USCRC(88) = &HFA01: USCRC(89) = &H3AC0: USCRC(90) = &H3B80: USCRC(91) = &HFB41

USCRC(92) = &H3900: USCRC(93) = &HF9C1: USCRC(94) = &HF881: USCRC(95) = &H3840

USCRC(96) = &H2800: USCRC(97) = &HE8C1: USCRC(98) = &HE981: USCRC(99) = &H2940

USCRC(100) = &HEB01: USCRC(101) = &H2BC0: USCRC(102) = &H2A80: USCRC(103) = &HEA41

USCRC(104) = &HEE01: USCRC(105) = &H2EC0: USCRC(106) = &H2F80: USCRC(107) = &HEF41

USCRC(108) = &H2D00: USCRC(109) = &HEDC1: USCRC(110) = &HEC81: USCRC(111) = &H2C40

USCRC(112) = &HE401: USCRC(113) = &H24C0: USCRC(114) = &H2580: USCRC(115) = &HE541

USCRC(116) = &H2700: USCRC(117) = &HE7C1: USCRC(118) = &HE681: USCRC(119) = &H2640

USCRC(120) = &H2200: USCRC(121) = &HE2C1: USCRC(122) = &HE381: USCRC(123) = &H2340

USCRC(124) = &HE101: USCRC(125) = &H21C0: USCRC(126) = &H2080: USCRC(127) = &HE041

USCRC(128) = &HA001: USCRC(129) = &H60C0: USCRC(130) = &H6180: USCRC(131) = &HA141

USCRC(132) = &H6300: USCRC(133) = &HA3C1: USCRC(134) = &HA281: USCRC(135) = &H6240

USCRC(136) = &H6600: USCRC(137) = &HA6C1: USCRC(138) = &HA781: USCRC(139) = &H6740

USCRC(140) = &HA501: USCRC(141) = &H65C0: USCRC(142) = &H6480: USCRC(143) = &HA441

USCRC(144) = &H6C00: USCRC(145) = &HACC1: USCRC(146) = &HAD81: USCRC(147) = &H6D40

USCRC(148) = &HAF01: USCRC(149) = &H6FC0: USCRC(150) = &H6E80: USCRC(151) = &HAE41

USCRC(152) = &HAA01: USCRC(153) = &H6AC0: USCRC(154) = &H6B80: USCRC(155) = &HAB41

USCRC(156) = &H6900: USCRC(157) = &HA9C1: USCRC(158) = &HA881: USCRC(159) = &H6840

USCRC(160) = &H7800: USCRC(161) = &HB8C1: USCRC(162) = &HB981: USCRC(163) = &H7940

USCRC(164) = &HBB01: USCRC(165) = &H7BC0: USCRC(166) = &H7A80: USCRC(167) = &HBA41

USCRC(168) = &HBE01: USCRC(169) = &H7EC0: USCRC(170) = &H7F80: USCRC(171) = &HBF41

USCRC(172) = &H7D00: USCRC(173) = &HBDC1: USCRC(174) = &HBC81: USCRC(175) = &H7C40

USCRC(176) = &HB401: USCRC(177) = &H74C0: USCRC(178) = &H7580: USCRC(179) = &HB541

USCRC(180) = &H7700: USCRC(181) = &HB7C1: USCRC(182) = &HB681: USCRC(183) = &H7640

USCRC(184) = &H7200: USCRC(185) = &HB2C1: USCRC(186) = &HB381: USCRC(187) = &H7340

USCRC(188) = &HB101: USCRC(189) = &H71C0: USCRC(190) = &H7080: USCRC(191) = &HB041

USCRC(192) = &H5000: USCRC(193) = &H90C1: USCRC(194) = &H9181: USCRC(195) = &H5140

USCRC(196) = &H9301: USCRC(197) = &H53C0: USCRC(198) = &H5280: USCRC(199) = &H9241

USCRC(200) = &H9601: USCRC(201) = &H56C0: USCRC(202) = &H5780: USCRC(203) = &H9741

USCRC(204) = &H5500: USCRC(205) = &H95C1: USCRC(206) = &H9481: USCRC(207) = &H5440

USCRC(208) = &H9C01: USCRC(209) = &H5CC0: USCRC(210) = &H5D80: USCRC(211) = &H9D41

USCRC(212) = &H5F00: USCRC(213) = &H9FC1: USCRC(214) = &H9E81: USCRC(215) = &H5E40

USCRC(216) = &H5A00: USCRC(217) = &H9AC1: USCRC(218) = &H9B81: USCRC(219) = &H5B40

USCRC(220) = &H9901: USCRC(221) = &H59C0: USCRC(222) = &H5880: USCRC(223) = &H9841

USCRC(224) = &H8801: USCRC(225) = &H48C0: USCRC(226) = &H4980: USCRC(227) = &H8941

USCRC(228) = &H4B00: USCRC(229) = &H8BC1: USCRC(230) = &H8A81: USCRC(231) = &H4A40

USCRC(232) = &H4E00: USCRC(233) = &H8EC1: USCRC(234) = &H8F81: USCRC(235) = &H4F40

USCRC(236) = &H8D01: USCRC(237) = &H4DC0: USCRC(238) = &H4C80: USCRC(239) = &H8C41

USCRC(240) = &H4400: USCRC(241) = &H84C1: USCRC(242) = &H8581: USCRC(243) = &H4540

USCRC(244) = &H8701: USCRC(245) = &H47C0: USCRC(246) = &H4680: USCRC(247) = &H8641

USCRC(248) = &H8201: USCRC(249) = &H42C0: USCRC(250) = &H4380: USCRC(251) = &H8341

USCRC(252) = &H4100: USCRC(253) = &H81C1: USCRC(254) = &H8081: USCRC(255) = &H4040

End Sub
~~~~