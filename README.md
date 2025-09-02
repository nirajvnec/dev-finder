<Overlay ...>
  <Overlay_Header>
    <h3><b>Preview Report</b></h3>
  </Overlay_Header>

  <Overlay_Body>
    {errorMessage ? (
      <div style={{ color: "red", padding: "20px", textAlign: "center" }}>
        {errorMessage}
      </div>
    ) : (
      <iframe
        className={styles.previewReportFrame}
        content="application/xml"
        src={reportUrl ?? ""}
      />
    )}
  </Overlay_Body>

  <Overlay_Footer>
    <Button onClick={handleCloseDialog}>Close</Button>
  </Overlay_Footer>
</Overlay>