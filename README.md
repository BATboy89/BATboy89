<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>BATify</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/react@18.2.0/umd/react.development.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/react-dom@18.2.0/umd/react-dom.development.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@babel/standalone@7.20.15/babel.min.js"></script>
  <style>
    body { background-color: #121212; color: #fff; font-family: Arial, sans-serif; }
    ::-webkit-scrollbar { width: 8px; }
    ::-webkit-scrollbar-track { background: #282828; }
    ::-webkit-scrollbar-thumb { background: #888; border-radius: 4px; }
    ::-webkit-scrollbar-thumb:hover { background: #b3b3b3; }
  </style>
</head>
<body>
  <div id="root"></div>
  <script type="text/babel">
    function App() {
      const [songs, setSongs] = React.useState([]);
      const [currentSong, setCurrentSong] = React.useState(null);
      const audioRef = React.useRef(new Audio());

      // Načítanie MP3 súborov
      const handleFileUpload = (event) => {
        const files = Array.from(event.target.files);
        const newSongs = files.map((file) => ({
          name: file.name,
          url: URL.createObjectURL(file),
        }));
        setSongs((prev) => [...prev, ...newSongs]);
      };

      // Prehrávanie skladby
      const playSong = (song) => {
        if (currentSong?.url !== song.url) {
          audioRef.current.pause();
          audioRef.current.src = song.url;
          setCurrentSong(song);
        }
        audioRef.current.play();
      };

      // Pozastavenie skladby
      const pauseSong = () => {
        audioRef.current.pause();
      };

      // Prepnutie na ďalšiu skladbu
      const playNext = () => {
        const currentIndex = songs.findIndex((s) => s.url === currentSong?.url);
        const nextIndex = (currentIndex + 1) % songs.length;
        if (songs[nextIndex]) {
          playSong(songs[nextIndex]);
        }
      };

      return (
        <div className="flex h-screen">
          {/* Bočný panel */}
          <div className="w-64 bg-black p-4 flex flex-col">
            <h1 className="text-2xl font-bold text-green-500 mb-8">BATify</h1>
            <ul className="space-y-2">
              <li className="text-gray-300 hover:text-white cursor-pointer">Domov</li>
              <li className="text-gray-300 hover:text-white cursor-pointer">Tvoje skladby</li>
            </ul>
            <div className="mt-auto">
              <label className="block text-gray-300 hover:text-white cursor-pointer">
                Nahrať MP3
                <input
                  type="file"
                  accept="audio/mp3"
                  multiple
                  className="hidden"
                  onChange={handleFileUpload}
                />
              </label>
            </div>
          </div>

          {/* Hlavný obsah */}
          <div className="flex-1 p-8 overflow-y-auto">
            <h2 className="text-2xl font-bold mb-4">Tvoje skladby</h2>
            <div className="grid gap-4">
              {songs.length === 0 ? (
                <p className="text-gray-400">Žiadne skladby. Nahraj MP3 súbory!</p>
              ) : (
                songs.map((song, index) => (
                  <div
                    key={index}
                    className="flex items-center p-4 bg-gray-800 rounded-lg hover:bg-gray-700 cursor-pointer"
                    onClick={() => playSong(song)}
                  >
                    <div className="w-12 h-12 bg-gray-600 rounded mr-4"></div>
                    <div>
                      <p className="text-white">{song.name}</p>
                      <p className="text-gray-400 text-sm">Lokálny súbor</p>
                    </div>
                  </div>
                ))
              )}
            </div>
          </div>

          {/* Prehrávač */}
          <div className="fixed bottom-0 w-full bg-gray-900 p-4 flex items-center justify-between">
            <div className="flex items-center">
              <div className="w-12 h-12 bg-gray-600 rounded mr-4"></div>
              <div>
                <p className="text-white">{currentSong ? currentSong.name : "Žiadna skladba"}</p>
                <p className="text-gray-400 text-sm">Lokálny súbor</p>
              </div>
            </div>
            <div className="flex space-x-4">
              <button
                className="text-gray-300 hover:text-white"
                onClick={currentSong ? (audioRef.current.paused ? () => playSong(currentSong) : pauseSong) : null}
              >
                {currentSong && !audioRef.current.paused ? "⏸" : "▶️"}
              </button>
              <button className="text-gray-300 hover:text-white" onClick={playNext}>
                ⏭
              </button>
            </div>
          </div>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
