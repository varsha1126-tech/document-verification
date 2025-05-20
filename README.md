# document-verification
frontend code using react and typescript for document verification system
import React, { useState, ChangeEvent } from "react";

type FileType = "pdf" | "jpeg" | "png";
const ACCEPTED_FORMATS = [
  "application/pdf",
  "image/jpeg",
  "image/png"
];

const getFileType = (file: File): FileType | null => {
  switch (file.type) {
    case "application/pdf":
      return "pdf";
    case "image/jpeg":
      return "jpeg";
    case "image/png":
      return "png";
    default:
      return null;
  }
};

const fakeAIVerify = async (file: File): Promise<{ valid: boolean; message: string }> => {
  // Simulate AI verification. Replace with real API call.
  return new Promise(resolve => {
    setTimeout(() => {
      if (file.size > 0 && ACCEPTED_FORMATS.includes(file.type)) {
        resolve({ valid: true, message: "The document is verified and appears authentic." });
      } else {
        resolve({ valid: false, message: "The document format or content is invalid." });
      }
    }, 2000);
  });
};

const UploadAndVerify: React.FC = () => {
  const [selectedFiles, setSelectedFiles] = useState<File[]>([]);
  const [verificationResults, setVerificationResults] = useState<
    { name: string; result: string; valid: boolean }[]
  >([]);
  const [uploading, setUploading] = useState(false);

  const handleFileChange = (e: ChangeEvent<HTMLInputElement>) => {
    if (!e.target.files) return;
    const filesArr = Array.from(e.target.files);
    const validFiles = filesArr.filter(file => ACCEPTED_FORMATS.includes(file.type));
    setSelectedFiles(validFiles);
  };

  const handleUploadAndVerify = async () => {
    setVerificationResults([]);
    setUploading(true);
    const results = [];
    for (const file of selectedFiles) {
      // You can add more checks here (file size, dimension, etc.)
      const aiResult = await fakeAIVerify(file);
      results.push({
        name: file.name,
        result: aiResult.message,
        valid: aiResult.valid
      });
    }
    setVerificationResults(results);
    setUploading(false);
  };

  return (
    <div
      style={{
        maxWidth: 500,
        margin: "40px auto",
        padding: 32,
        borderRadius: 12,
        boxShadow: "0 2px 16px #ddd",
        background: "#fff"
      }}
    >
      <h2>Upload and Verify Document</h2>
      <p>
        Supported formats: <strong>PDF, JPEG, PNG</strong>
      </p>
      <input
        type="file"
        accept={ACCEPTED_FORMATS.join(",")}
        multiple
        onChange={handleFileChange}
        disabled={uploading}
        style={{ marginBottom: 20 }}
      />
      <div>
        {selectedFiles.length > 0 && (
          <ul style={{ paddingLeft: 18 }}>
            {selectedFiles.map(file => (
              <li key={file.name}>
                {file.name} <span style={{ fontSize: 12, color: "#888" }}>({file.type})</span>
              </li>
            ))}
          </ul>
        )}
      </div>
      <button
        onClick={handleUploadAndVerify}
        disabled={selectedFiles.length === 0 || uploading}
        style={{
          marginTop: 14,
          padding: "10px 28px",
          fontSize: 16,
          borderRadius: 4,
          border: "none",
          background: "#0070f3",
          color: "#fff",
          cursor: uploading ? "not-allowed" : "pointer"
        }}
      >
        {uploading ? "Verifying..." : "Upload & Verify"}
      </button>
      {verificationResults.length > 0 && (
        <div style={{ marginTop: 30 }}>
          <h3>Verification Results</h3>
          <ul style={{ paddingLeft: 18 }}>
            {verificationResults.map(res => (
              <li
                key={res.name}
                style={{
                  color: res.valid ? "green" : "red",
                  marginBottom: 6
                }}
              >
                <strong>{res.name}</strong>: {res.result}
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
};

export default UploadAndVerify;