import React, { useEffect, useMemo, useState } from "react";
import { BadgeCheck, XCircle, Share2, Printer, Copy, Search } from "lucide-react";
import QRCode from "qrcode.react";

const MOCK_DB: Record<string, any> = {
  "60B4FAC2F4A1449480023D81C23A600B2C216B05CC": {
    candidateName: "Aw Jhun Hoong",
    idNo: "010101-14-1234",
    certificate: "Sijil Pelajaran Malaysia (SPM)",
    issuingBody: "Lembaga Peperiksaan, KPM",
    batch: "2023",
    indexNo: "KPM/LP/SPM/2023/123456",
    dateIssued: "2024-03-15",
    status: "SAH / VALID",
  },
};

async function mockLookup(id: string) {
  await new Promise((r) => setTimeout(r, 200));
  const payload = MOCK_DB[id.toUpperCase()];
  return payload ? { valid: true, payload } : { valid: false };
}

export default function VerificationPage() {
  const [id, setId] = useState("");
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState<any>(null);

  useEffect(() => {
    if (!id) return;
    setLoading(true);
    mockLookup(id).then((res) => {
      setResult(res);
      setLoading(false);
    });
  }, [id]);

  const shareUrl = useMemo(() => window.location.origin + "/qr/" + id, [id]);

  function submitId() {
    if (!input.trim()) return;
    setId(input.trim());
    setResult(null);
  }

  return (
    <div className="min-h-screen bg-gray-50 text-gray-800 p-6">
      <h1 className="text-xl font-bold mb-4">Certificate Verification</h1>

      {/* Input */}
      <div className="flex gap-2 mb-6">
        <input
          type="text"
          placeholder="Enter Verification ID"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          className="flex-1 p-2 border rounded"
        />
        <button onClick={submitId} className="px-4 py-2 bg-blue-600 text-white rounded">
          <Search className="w-4 h-4 inline-block mr-1" /> Verify
        </button>
      </div>

      {/* Result */}
      <div className="p-4 border rounded bg-white shadow">
        {loading && <p className="text-gray-500">Checking...</p>}
        {!loading && result && result.valid && (
          <div>
            <div className="flex items-center text-green-600 mb-4">
              <BadgeCheck className="w-6 h-6 mr-2" />
              <span className="font-semibold">Valid Record Found</span>
            </div>
            <ul className="space-y-2 text-sm">
              <li><strong>Name:</strong> {result.payload.candidateName}</li>
              <li><strong>ID No.:</strong> {result.payload.idNo}</li>
              <li><strong>Certificate:</strong> {result.payload.certificate}</li>
              <li><strong>Issuing Body:</strong> {result.payload.issuingBody}</li>
              <li><strong>Batch:</strong> {result.payload.batch}</li>
              <li><strong>Index No.:</strong> {result.payload.indexNo}</li>
              <li><strong>Date Issued:</strong> {result.payload.dateIssued}</li>
              <li><strong>Status:</strong> {result.payload.status}</li>
            </ul>
            <div className="mt-4">
              <QRCode value={shareUrl} size={128} />
            </div>
          </div>
        )}
        {!loading && result && !result.valid && (
          <div className="flex items-center text-red-600">
            <XCircle className="w-6 h-6 mr-2" />
            <span>No record found for this ID.</span>
          </div>
        )}
      </div>
    </div>
  );
}
