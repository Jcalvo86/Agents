# Componente: ImageUploader

Componente premium de carga de archivos e imágenes con soporte para arrastrar y soltar, estadísticas en tiempo real y alertas de reintento.

### Ubicación del Código
- **React Component**: [ImageUploader.jsx](file:///C:/andreia/Web-sueno-travel/controlpanel/components/ImageUploader.jsx)
- **Carga Utility**: [upload.js](file:///C:/andreia/Web-sueno-travel/controlpanel/utils/upload.js)

---

## Modo de Uso (React)

```jsx
import ImageUploader from '../components/ImageUploader.jsx';

<ImageUploader
  value={formData.imageUrl || ''}
  onChange={(url) => setFormData({ ...formData, imageUrl: url })}
  label="Imagen del Viaje"
  allowedTypes={['image/png', 'image/jpeg', 'image/webp', 'image/gif']} // Opcional
  maxSizeBytes={10 * 1024 * 1024} // Opcional (10MB por defecto)
/>
```

---

## Lógica Interna de Progreso (XHR)

El cargador delega la subida a `uploadFileWithProgress` en `upload.js`. Esta función utiliza `XMLHttpRequest` para monitorizar el progreso real de subida en Firebase y Supabase mediante el evento `xhr.upload.onprogress`.

### Cálculo de Estadísticas (MB/s & Tiempo Restante)
En cada evento de progreso, el componente calcula:
1. **Velocidad**: `loadedBytes / elapsedSeconds`.
2. **Tiempo Restante (ETA)**: `remainingBytes / bytesPerSecond`.

---

## Comportamiento y Feedback Visual

1. **Arrastrar y Soltar (Drag & Drop)**:
   - Al arrastrar un archivo sobre la zona activa, cambia el borde del contenedor a un color destacado y el fondo se atenúa (efecto hover activo) para guiar al usuario.
2. **Barra de Progreso y Estadísticas de Subida**:
   - Durante el proceso de subida, se muestra una barra animada con el porcentaje exacto completado, junto con la velocidad de subida calculada en tiempo real (MB/s) y el tiempo restante estimado (ETA).
3. **Alertas de Error y Reintento**:
   - Si la carga falla (problema de red, tamaño excedido, formato no permitido), el componente muestra una alerta de error integrada dentro del contenedor y habilita un botón de "Reintentar" para reiniciar el proceso de inmediato.
   - Si el archivo excede los límites (por ejemplo, mayor a 10MB o tipo no permitido), muestra alertas de error inline preventivas sin enviar la solicitud de red.
4. **Previsualización de Imagen y Controles**:
   - Una vez subido con éxito, muestra una miniatura de alta resolución de la imagen.
   - Cuenta con controles flotantes superpuestos para reemplazar o eliminar la imagen de forma sencilla.

---

## Código Completo de Producción

### 1. Utilidad de Carga (`controlpanel/utils/upload.js`)

```javascript
export function uploadFileWithProgress(file, onProgress) {
  return new Promise((resolve, reject) => {
    const config = window.DataSourceConfig ? window.DataSourceConfig.getConfig() : window.CRM_CONFIG;
    if (!config) return reject(new Error("No se pudo cargar la configuración de base de datos."));

    const storageProvider = config.storageProvider || 'uploadthing';

    // Firebase
    if (storageProvider === 'firebase' && config.firebase) {
      try {
        const { projectId } = config.firebase;
        const fileExt = file.name.split('.').pop();
        const fileName = `uploads/${Date.now()}-${Math.random().toString(36).substring(2, 8)}.${fileExt}`;
        const bucketName = `${projectId}.appspot.com`;
        const uploadUrl = `https://firebasestorage.googleapis.com/v0/b/${bucketName}/o?name=${encodeURIComponent(fileName)}`;

        const xhr = new XMLHttpRequest();
        xhr.open('POST', uploadUrl, true);
        xhr.setRequestHeader('Content-Type', file.type);

        if (onProgress) {
          xhr.upload.onprogress = (e) => {
            if (e.lengthComputable) onProgress({ loaded: e.loaded, total: e.total });
          };
        }

        xhr.onload = () => {
          if (xhr.status >= 200 && xhr.status < 300) {
            try {
              const data = JSON.parse(xhr.responseText);
              const downloadToken = data.downloadTokens;
              resolve(`https://firebasestorage.googleapis.com/v0/b/${bucketName}/o/${encodeURIComponent(fileName)}?alt=media${downloadToken ? `&token=${downloadToken}` : ''}`);
            } catch (err) {
              reject(new Error("Error al procesar la respuesta de Firebase Storage."));
            }
          } else {
            reject(new Error(`Firebase Storage respondió con error ${xhr.status}: ${xhr.statusText}`));
          }
        };

        xhr.onerror = () => reject(new Error("Error de red al subir a Firebase Storage."));
        xhr.send(file);
        return;
      } catch (e) {
        console.warn(e);
      }
    }

    // Supabase
    if (storageProvider === 'supabase' && config.supabase && config.supabase.url && config.supabase.anonKey) {
      try {
        const cleanUrl = config.supabase.url.replace(/\/$/, '');
        const bucketName = 'images';
        const fileExt = file.name.split('.').pop();
        const fileName = `${Date.now()}-${Math.random().toString(36).substring(2, 8)}.${fileExt}`;
        const uploadUrl = `${cleanUrl}/storage/v1/object/${bucketName}/${fileName}`;

        const xhr = new XMLHttpRequest();
        xhr.open('POST', uploadUrl, true);
        xhr.setRequestHeader('apikey', config.supabase.anonKey);
        xhr.setRequestHeader('Authorization', `Bearer ${config.supabase.anonKey}`);
        xhr.setRequestHeader('Content-Type', file.type);

        if (onProgress) {
          xhr.upload.onprogress = (e) => {
            if (e.lengthComputable) onProgress({ loaded: e.loaded, total: e.total });
          };
        }

        xhr.onload = () => {
          if (xhr.status >= 200 && xhr.status < 300) {
            resolve(`${cleanUrl}/storage/v1/object/public/${bucketName}/${fileName}`);
          } else {
            reject(new Error(`Supabase Storage respondió con error ${xhr.status}: ${xhr.statusText}`));
          }
        };

        xhr.onerror = () => reject(new Error("Error de red al subir a Supabase Storage."));
        xhr.send(file);
        return;
      } catch (e) {
        console.warn(e);
      }
    }

    // Fallback UploadThing (simulado)
    let simulatedInterval;
    if (onProgress) {
      let progress = 0;
      simulatedInterval = setInterval(() => {
        progress += (100 - progress) * 0.1;
        if (progress > 95) progress = 95;
        onProgress({ loaded: Math.round((progress / 100) * file.size), total: file.size });
      }, 250);
    }

    uploadThingUploader.uploadFiles("imageUploader", { files: [file] })
      .then(res => {
        if (simulatedInterval) clearInterval(simulatedInterval);
        if (onProgress) onProgress({ loaded: file.size, total: file.size });
        if (res && res[0]) resolve(res[0].url);
        else reject(new Error("No response from UploadThing"));
      })
      .catch(err => {
        if (simulatedInterval) clearInterval(simulatedInterval);
        reject(err);
      });
  });
}
```

### 2. Componente React (`controlpanel/components/ImageUploader.jsx`)

```jsx
import React, { useState, useRef, useEffect } from 'react';
import { uploadFileWithProgress } from '../utils/upload.js';

export default function ImageUploader({ value, onChange, label = 'Imagen', allowedTypes = ['image/png', 'image/jpeg', 'image/webp', 'image/gif'], maxSizeBytes = 10 * 1024 * 1024 }) {
  const [isDragOver, setIsDragOver] = useState(false);
  const [uploadStatus, setUploadStatus] = useState('idle'); // idle, uploading, error, success
  const [progress, setProgress] = useState(0);
  const [uploadStats, setUploadStats] = useState({ speed: '0 MB/s', timeRemaining: '' });
  const [errorMessage, setErrorMessage] = useState('');
  const [currentFile, setCurrentFile] = useState(null);
  
  const fileInputRef = useRef(null);
  const startTimeRef = useRef(null);

  useEffect(() => {
    if (value && uploadStatus === 'idle') {
      setUploadStatus('success');
    } else if (!value && uploadStatus === 'success') {
      setUploadStatus('idle');
    }
  }, [value]);

  const handleDragOver = (e) => { e.preventDefault(); setIsDragOver(true); };
  const handleDragLeave = () => setIsDragOver(false);
  const handleDrop = (e) => {
    e.preventDefault();
    setIsDragOver(false);
    const file = e.dataTransfer.files[0];
    if (file) processFile(file);
  };
  const handleFileChange = (e) => {
    const file = e.target.files[0];
    if (file) processFile(file);
  };
  const triggerSelectFile = () => fileInputRef.current?.click();

  const processFile = (file) => {
    if (!allowedTypes.includes(file.type)) {
      setUploadStatus('error');
      setErrorMessage(`Tipo de archivo no permitido. Tipos válidos: ${allowedTypes.map(t => t.split('/')[1].toUpperCase()).join(', ')}`);
      return;
    }
    if (file.size > maxSizeBytes) {
      setUploadStatus('error');
      setErrorMessage(`El archivo es demasiado grande. El límite es ${(maxSizeBytes / (1024 * 1024))}MB.`);
      return;
    }
    setCurrentFile(file);
    startUpload(file);
  };

  const startUpload = (file) => {
    setUploadStatus('uploading');
    setProgress(0);
    setErrorMessage('');
    setUploadStats({ speed: '0 KB/s', timeRemaining: 'calculando...' });
    startTimeRef.current = Date.now();

    uploadFileWithProgress(file, ({ loaded, total }) => {
      const pct = Math.round((loaded / total) * 100);
      setProgress(pct);
      const elapsedSeconds = (Date.now() - startTimeRef.current) / 1000;
      if (elapsedSeconds > 0) {
        const bytesPerSecond = loaded / elapsedSeconds;
        let speedStr = bytesPerSecond > 1024 * 1024 
          ? `${(bytesPerSecond / (1024 * 1024)).toFixed(1)} MB/s` 
          : `${(bytesPerSecond / 1024).toFixed(0)} KB/s`;

        const remainingBytes = total - loaded;
        let timeRemainingStr = pct === 100 ? 'Completado' : `${Math.ceil(remainingBytes / bytesPerSecond)}s restantes`;
        setUploadStats({ speed: speedStr, timeRemaining: timeRemainingStr });
      }
    })
      .then((url) => { setUploadStatus('success'); onChange(url); })
      .catch((err) => { setUploadStatus('error'); setErrorMessage(err.message || 'Error al subir el archivo'); });
  };

  const handleRetry = () => currentFile && startUpload(currentFile);
  const handleDelete = () => {
    onChange('');
    setUploadStatus('idle');
    setCurrentFile(null);
    setProgress(0);
    if (fileInputRef.current) fileInputRef.current.value = '';
  };

  const formatBytes = (bytes) => {
    if (!bytes) return '0 Bytes';
    const k = 1024;
    const sizes = ['Bytes', 'KB', 'MB', 'GB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return parseFloat((bytes / Math.pow(k, i)).toFixed(1)) + ' ' + sizes[i];
  };

  const getFileDisplayInfo = () => {
    if (currentFile) {
      return { name: currentFile.name, size: formatBytes(currentFile.size), type: currentFile.type.split('/')[1].toUpperCase() };
    }
    if (value) {
      try {
        const decodedUrl = decodeURIComponent(value);
        const namePart = decodedUrl.split('/').pop().split('?')[0];
        const ext = namePart.split('.').pop().toUpperCase();
        return { name: namePart.length > 30 ? namePart.substring(0, 27) + '...' + ext : namePart, size: 'Remoto', type: ext || 'IMAGEN' };
      } catch (e) {
        return { name: 'Archivo cargado', size: 'Remoto', type: 'IMAGEN' };
      }
    }
    return { name: '', size: '', type: '' };
  };

  const info = getFileDisplayInfo();

  return (
    <div className="w-full flex flex-col gap-2">
      {label && <span className="font-label-md block" style={{ color: 'var(--on-surface-variant)' }}>{label}</span>}
      <input type="file" ref={fileInputRef} onChange={handleFileChange} accept={allowedTypes.join(',')} className="hidden" />

      <div className="w-full">
        {/* 1. Idle or dragging */}
        {(uploadStatus === 'idle' || isDragOver) && (
          <div
            onDragOver={handleDragOver} onDragLeave={handleDragLeave} onDrop={handleDrop} onClick={triggerSelectFile}
            className={`border-2 border-dashed rounded-xl p-8 flex flex-col items-center justify-center gap-3 cursor-pointer transition-all duration-200 min-h-[170px]
              ${isDragOver 
                ? 'border-[var(--success)] bg-[color-mix(in_srgb,var(--success)_5%,transparent)] scale-[1.01]' 
                : 'border-[var(--outline)] hover:border-[var(--primary)] hover:bg-[color-mix(in_srgb,var(--primary)_3%,transparent)]'
              }`}
          >
            <div className={`p-3 rounded-full flex items-center justify-center transition-colors duration-200 ${isDragOver ? 'bg-[color-mix(in_srgb,var(--success)_15%,transparent)] text-[var(--success)]' : 'bg-[var(--surface-container-high)] text-[var(--on-surface-variant)]'}`}>
              <span className="material-symbols-outlined text-3xl">{isDragOver ? 'cloud_done' : 'cloud_upload'}</span>
            </div>
            <div className="text-center">
              <p className="font-body-md font-semibold text-[var(--on-surface)]">{isDragOver ? '¡Suelta para cargar la imagen!' : 'Arrastra tu archivo aquí o haz clic para buscar'}</p>
              <p className="font-body-xs mt-1" style={{ color: 'var(--on-surface-variant)' }}>Formatos permitidos: {allowedTypes.map(t => t.split('/')[1].toUpperCase()).join(', ')} (máx. {formatBytes(maxSizeBytes)})</p>
            </div>
          </div>
        )}

        {/* 2. Uploading */}
        {uploadStatus === 'uploading' && (
          <div className="border border-[var(--outline-variant)] rounded-xl p-5 bg-[var(--surface-container-low)] space-y-4">
            <div className="flex items-center justify-between gap-4">
              <div className="flex items-center gap-3 overflow-hidden">
                <span className="material-symbols-outlined text-[var(--primary)] text-2xl shrink-0 animate-pulse">upload_file</span>
                <div className="overflow-hidden">
                  <p className="font-body-sm font-semibold truncate text-[var(--on-surface)]" title={info.name}>{info.name}</p>
                  <p className="font-body-xs text-[var(--on-surface-variant)]">{info.size} • {progress}% completado</p>
                </div>
              </div>
              <div className="shrink-0 flex flex-col items-end">
                <span className="font-body-sm font-bold text-[var(--primary)]">{uploadStats.speed}</span>
                <span className="font-body-xs text-[var(--on-surface-variant)]">{uploadStats.timeRemaining}</span>
              </div>
            </div>
            <div className="w-full flex items-center gap-3">
              <div className="relative w-full h-2 bg-[var(--outline-variant)] rounded-full overflow-hidden">
                <div className="h-full bg-[var(--primary)] rounded-full transition-all duration-150 ease-out" style={{ width: `${progress}%` }} />
              </div>
              <span className="font-body-xs font-semibold w-8 text-right text-[var(--on-surface)]">{progress}%</span>
            </div>
          </div>
        )}

        {/* 3. Error */}
        {uploadStatus === 'error' && (
          <div className="border-2 border-dashed border-[var(--outline)] rounded-xl p-5 flex flex-col items-center justify-center gap-4 min-h-[170px]">
            <div className="w-full bg-[color-mix(in_srgb,var(--error)_10%,transparent)] border border-[color-mix(in_srgb,var(--error)_20%,transparent)] rounded-lg p-4 flex items-start gap-3">
              <span className="material-symbols-outlined text-[var(--error)] shrink-0 mt-0.5">error</span>
              <div className="flex-1 overflow-hidden">
                <p className="font-body-sm font-semibold text-[var(--error)]">Error al cargar el archivo</p>
                <p className="font-body-xs text-[var(--on-surface-variant)] mt-1 break-words">{errorMessage}</p>
              </div>
              {currentFile && (
                <button
                  type="button" onClick={handleRetry}
                  className="btn-secondary text-xs shrink-0 flex items-center gap-1 py-1 px-3 border border-[var(--outline)] rounded-md font-semibold hover:bg-[var(--surface-container-high)]"
                  style={{ height: 'auto' }}
                >
                  <span className="material-symbols-outlined text-sm">replay</span>Reintentar
                </button>
              )}
            </div>
            <button type="button" onClick={handleDelete} className="text-xs text-[var(--primary)] hover:underline font-semibold">Volver a intentar con otro archivo</button>
          </div>
        )}

        {/* 4. Success */}
        {uploadStatus === 'success' && value && (
          <div className="border border-[var(--outline-variant)] rounded-xl p-4 bg-[var(--surface-container-low)] flex flex-col sm:flex-row items-center sm:items-start gap-4">
            <div className="w-24 h-24 rounded-lg overflow-hidden shrink-0 border border-[var(--outline-variant)] bg-[var(--surface-container)] flex items-center justify-center">
              <img
                src={value} alt="Miniatura de carga" className="w-full h-full object-cover"
                onError={(e) => {
                  e.target.onerror = null;
                  e.target.src = 'https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=150&auto=format&fit=crop&q=60';
                }}
              />
            </div>
            <div className="flex-1 w-full min-w-0 flex flex-col justify-between self-stretch py-1">
              <div className="min-w-0">
                <p className="font-body-md font-bold truncate text-[var(--on-surface)]" title={info.name}>{info.name}</p>
                <p className="font-body-xs text-[var(--on-surface-variant)] mt-0.5">Tipo: <span className="font-semibold text-[var(--on-surface)]">{info.type}</span> • Tamaño: <span className="font-semibold text-[var(--on-surface)]">{info.size}</span></p>
              </div>
              <div className="flex items-center gap-4 mt-4 sm:mt-0 pt-2 border-t border-[var(--outline-variant)] sm:border-none">
                <button type="button" onClick={triggerSelectFile} className="text-xs text-[var(--primary)] hover:underline flex items-center gap-1 font-semibold border-none bg-transparent cursor-pointer">
                  <span className="material-symbols-outlined text-base">cached</span>Reemplazar
                </button>
                <button type="button" onClick={handleDelete} className="text-xs text-[var(--error)] hover:underline flex items-center gap-1 font-semibold border-none bg-transparent cursor-pointer">
                  <span className="material-symbols-outlined text-base">delete</span>Eliminar
                </button>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
```
