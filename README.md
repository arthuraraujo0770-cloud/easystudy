# easystudy
import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

export default function EasyStudyApp(){
  const [user,setUser]=useState(null);
  const [coins,setCoins]=useState(0);
  const [questions,setQuestions]=useState(20);
  const [page,setPage]=useState("login");
  const [visits,setVisits]=useState(0);
  const [registrations,setRegistrations]=useState(0);
  const [uploadedImage,setUploadedImage]=useState(null);

  useEffect(()=>{
    const v = Number(localStorage.getItem("visits")||0)+1;
    localStorage.setItem("visits",v);
    setVisits(v);

    const r = Number(localStorage.getItem("registrations")||0);
    setRegistrations(r);
  },[]);

  function speakGreeting(name){
    const hour = new Date().getHours();
    let text="";

    if(hour<12) text=`Buenos días ${name}, bienvenido a EasyStudy. Estoy aquí para ayudarte a estudiar.`;
    else if(hour<18) text=`Buenas tardes ${name}. ¿Qué materia quieres practicar hoy en EasyStudy?`;
    else text=`Buenas noches ${name}. Podemos repasar algo antes de descansar.`;

    const voices = speechSynthesis.getVoices();
    const spanishVoice = voices.find(v => v.lang.includes("es")) || voices[0];

    const msg = new SpeechSynthesisUtterance(text);
    msg.voice = spanishVoice;
    msg.rate = 0.95;
    msg.pitch = 1;
    msg.volume = 1;

    speechSynthesis.speak(msg);
  }

  function login(e){
    e.preventDefault();
    const name=e.target.name.value;

    const r = Number(localStorage.getItem("registrations")||0)+1;
    localStorage.setItem("registrations",r);

    setRegistrations(r);
    setUser({name});
    setPage("dashboard");
    setTimeout(()=>speakGreeting(name),500);
  }

  function addCoins(){
    if(coins>=25) return;
    setCoins(Math.min(coins+5,25));
  }

  function buyQuestion(){
    if(coins>=2){
      setCoins(coins-2);
      setQuestions(q=>q+1);
    }
  }

  function handleImageUpload(e){
    const file=e.target.files[0];
    if(file){
      const url=URL.createObjectURL(file);
      setUploadedImage(url);
      if(questions>0) setQuestions(q=>q-1);
    }
  }

  if(page==="login"){
    return (
      <div className="min-h-screen flex items-center justify-center bg-blue-100">
        <Card className="p-6 w-80">
          <CardContent>
            <h1 className="text-2xl font-bold mb-2 text-center">EasyStudy</h1>
            <p className="text-center mb-4">Estudia fácil, aprende mejor</p>

            <form onSubmit={login} className="flex flex-col gap-3">
              <input name="name" placeholder="Nombre" className="border p-2 rounded" />
              <input placeholder="Correo" className="border p-2 rounded" />
              <input type="password" placeholder="Contraseña" className="border p-2 rounded" />
              <input placeholder="Teléfono" className="border p-2 rounded" />
              <Button type="submit">Entrar</Button>
            </form>

            <div className="mt-4 text-center text-sm">
              <p>👥 Visitas totales: {visits}</p>
              <p>📝 Usuarios registrados: {registrations}</p>
            </div>
          </CardContent>
        </Card>
      </div>
    );
  }

  if(page==="games"){
    return (
      <div className="p-6">
        <h2 className="text-2xl font-bold mb-4">Zona de Juegos</h2>
        <p>Pepes Coins: {coins}</p>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mt-4">

          <Card>
            <CardContent className="p-4">
              <h3 className="font-bold">🐟 Pez Inteligente</h3>
              <p>Juego estilo snake con pez.</p>
              <Button onClick={addCoins}>Completar nivel (+5)</Button>
            </CardContent>
          </Card>

          <Card>
            <CardContent className="p-4">
              <h3 className="font-bold">🍓 Fruta Match</h3>
              <p>Combina frutas tipo puzzle.</p>
              <Button onClick={addCoins}>Completar nivel (+5)</Button>
            </CardContent>
          </Card>

          <Card>
            <CardContent className="p-4">
              <h3 className="font-bold">🧠 Reto Visual</h3>
              <p>Preguntas con ilustraciones.</p>
              <Button onClick={addCoins}>Completar nivel (+5)</Button>
            </CardContent>
          </Card>

        </div>

        <Button className="mt-6" onClick={()=>setPage("dashboard")}>Volver</Button>
      </div>
    );
  }

  if(page==="upload"){
    return (
      <div className="p-6">
        <h2 className="text-2xl font-bold mb-4">Resolver tarea con foto</h2>
        <p className="mb-4">Sube una foto de tu tarea y EasyStudy te ayudará a resolverla.</p>

        <input type="file" accept="image/*" onChange={handleImageUpload} />

        {uploadedImage && (
          <div className="mt-6">
            <p className="mb-2">Imagen subida:</p>
            <img src={uploadedImage} alt="tarea" className="max-w-sm rounded-xl shadow" />

            <Card className="mt-4">
              <CardContent className="p-4">
                <p className="font-bold">Explicación simulada:</p>
                <p>La plataforma analizaría la imagen y mostraría aquí la solución paso a paso.</p>
              </CardContent>
            </Card>
          </div>
        )}

        <Button className="mt-6" onClick={()=>setPage("dashboard")}>Volver</Button>
      </div>
    );
  }

  return (
    <div className="p-6">
      <div className="flex items-center gap-4 mb-2">
        <div className="text-5xl">🪿</div>
        <div>
          <h1 className="text-3xl font-bold">Bienvenido {user?.name}</h1>
          <p className="text-sm opacity-70">Pepe el Ganso te acompaña a estudiar en EasyStudy</p>
        </div>
      </div>
      <p className="mb-2">Preguntas restantes: {questions}</p>
      <p className="mb-4">Pepes Coins: {coins}</p>

      <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
        <Button onClick={()=>setQuestions(q=>q-1)}>✏️ Hacer pregunta</Button>
        <Button onClick={()=>setPage("upload")}>📷 Subir foto de tarea</Button>
        <Button onClick={()=>setPage("games")}>🎮 Minijuegos</Button>
        <Button onClick={buyQuestion}>🪙 Comprar pregunta (2)</Button>
        <Button>🧮 Calculadora científica</Button>
        <Button>📏 Conversión medidas</Button>
        <Button>💱 Conversión monedas</Button>
        <Button>🌍 Reloj mundial</Button>
        <Button>🧠 Mapas mentales</Button>
      </div>

      <div className="mt-8">
        <h2 className="text-xl font-bold">Consulta de pago Premium</h2>
        <p>$5 al mes - preguntas ilimitadas y asistente de voz</p>
        <a href="https://wa.me/" target="_blank">
          <Button className="mt-2">Consulta de pago</Button>
        </a>
      </div>

      <div className="mt-10 text-sm opacity-70">
        <p>👥 Visitas totales: {visits}</p>
        <p>📝 Usuarios registrados: {registrations}</p>
      </div>
    </div>
  );
}
