const [reportUrl, setReportUrl] = useState<string | null>(null);
const [errorMessage, setErrorMessage] = useState<string | null>(null);

useEffect(() => {
  const func = async () => {
    try {
      const cobDate = await referenceDataService.getCurrentCobDate();
      const formattedCobDate = moment(cobDate, "YYYYMMDD").format("YYYY-MM-DD");

      const response = await riskMonitoringService.getUrlForPreview(
        props.actionRowData?.reportKey,
        formattedCobDate
      );

      // ✅ Simple URL check
      const urlPattern = /^https?:\/\/.+/i;
      if (urlPattern.test(response)) {
        setReportUrl(response);
        setErrorMessage(null);
      } else {
        setReportUrl(null);
        setErrorMessage(response); // something like "PBI report not found"
      }
    } catch (err) {
      setReportUrl(null);
      setErrorMessage("Failed to fetch report. Please try again later.");
    }
  };
  func();
}, [props.actionRowData]);