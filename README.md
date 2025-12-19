String vKriter;

String rvsFlag = (pRvsFlag == null || pRvsFlag.trim().isEmpty())
        ? "P"
        : pRvsFlag.trim();

boolean isReverseP = "P".equals(rvsFlag);
boolean isHsriad = "HSRIAD".equals(pTrxCode);

if (isReverseP) {
    vKriter = isHsriad ? "C" : "D";
} else {
    vKriter = isHsriad ? "D" : "C";
}
