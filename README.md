const [reloadReportStatus, setReloadReportStatus] = useState(0);

useEffect(() => {
  const loadReportStatus = async () => {
    // your existing load logic
  };

  loadReportStatus();
}, [reloadReportStatus]);

// Pass this setter function directly to EmailConfig
<EmailConfig setReloadReportStatus={setReloadReportStatus} />