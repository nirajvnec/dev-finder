// Apply StandardDeviationCoeff default value if null or zero

if ((threshold.StandardDeviationCoeff ?? 0) == 0)
{
    threshold.StandardDeviationCoeff = 1;
}


StandardDeviationCoeff to 1 if null or zero in threshold payload



git checkout -b feature/default-standard-deviation-coeff
