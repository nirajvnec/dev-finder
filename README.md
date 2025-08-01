const [reloadReportStatus, setReloadReportStatus] = useState(0);

useEffect(() => {
  const loadReportStatus = async () => {
    // your existing load logic
  };

  loadReportStatus();
}, [reloadReportStatus]);

// Pass this setter function directly to EmailConfig
<EmailConfig setReloadReportStatus={setReloadReportStatus} />




interface EmailConfigProps {
  setReloadReportStatus: React.Dispatch<React.SetStateAction<number>>;
  // ...other props
}



rootContext?.showSuccess("Email sent successfully!");
props.setReloadReportStatus(prev => prev + 1);