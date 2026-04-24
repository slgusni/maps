---
title: "Demo Animation"
---

{{< rawhtml >}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Joint Island Landing Campaign</title>
    <style>
        * { box-sizing: border-box; }
        body { margin: 0; padding: 10px; font-family: Arial, sans-serif; background: #e8f4f8; display: flex; flex-direction: column; align-items: center; }
        .container { width: 100%; max-width: 1200px; display: flex; flex-direction: column; align-items: center; }
        #canvasContainer { width: 100%; position: relative; border: 2px solid #333; background: linear-gradient(180deg, #a8d5e2 0%, #76b8d4 100%); box-shadow: 0 4px 20px rgba(0,0,0,0.3); }
        #canvas { display: block; width: 100%; height: auto; }
        .controls { margin-top: 20px; display: flex; gap: 15px; align-items: center; flex-wrap: wrap; justify-content: center; }
        button { padding: 12px 24px; font-size: 16px; background: #c0392b; color: white; border: none; border-radius: 5px; cursor: pointer; transition: background 0.3s; }
        button:hover { background: #a93226; }
        h1 { color: #2c3e50; margin-bottom: 10px; text-align: center; font-size: clamp(1.2rem, 4vw, 2rem); }
        @media (max-width: 768px) { body { padding: 5px; } button { padding: 10px 20px; font-size: 14px; } }
    </style>
</head>
<body>
    <div class="container">
        <h1>Joint Island Landing Campaign - Sequential Phases</h1>
        <div id="canvasContainer">
            <canvas id="canvas" width="1200" height="900"></canvas>
        </div>
        <div class="controls">
            <button id="nextBtn">Next Phase</button>
            <button id="resetBtn">Reset</button>
        </div>
    </div>
    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const nextBtn = document.getElementById('nextBtn');
        const resetBtn = document.getElementById('resetBtn');
        let currentPhase = 0;
        let scale = 1;
        function resizeCanvas() {
            const container = document.getElementById('canvasContainer');
            const rect = container.getBoundingClientRect();
            scale = rect.width / 1200;
            canvas.style.width = rect.width + 'px';
            canvas.style.height = (rect.width * 0.75) + 'px';
            drawPhase(currentPhase);
        }
        window.addEventListener('resize', resizeCanvas);
        window.addEventListener('load', resizeCanvas);
        const island = { points: [{x:700,y:450},{x:850,y:430},{x:950,y:470},{x:1000,y:550},{x:980,y:650},{x:900,y:730},{x:800,y:750},{x:700,y:730},{x:650,y:650},{x:640,y:550}] };
        const outlyingIslands = [[{x:480,y:330},{x:530,y:320},{x:550,y:355},{x:495,y:365}],[{x:580,y:380},{x:630,y:375},{x:645,y:405},{x:590,y:415}]];
        const cities = [{name:"Napool",x:720,y:620},{name:"Micester",x:820,y:710},{name:"Ekadale",x:920,y:590},{name:"Ylirgov",x:850,y:800}];
        const ships = [{type:"Type 003 Fujian",x:800,y:80},{type:"Amphibious Ship",x:850,y:120},{type:"Type 075 LHD",x:220,y:280},{type:"Type 071 LPD",x:1050,y:730}];
        function drawIsland(points,color='#e8d7a8',strokeColor='#b89968'){ctx.fillStyle=color;ctx.beginPath();ctx.moveTo(points[0].x,points[0].y);for(let i=1;i<points.length;i++){ctx.lineTo(points[i].x,points[i].y);}ctx.closePath();ctx.fill();ctx.strokeStyle=strokeColor;ctx.lineWidth=2;ctx.stroke();ctx.strokeStyle='#5a9fb8';ctx.lineWidth=4;ctx.stroke();}
        function drawShip(x,y,type){ctx.save();ctx.fillStyle='#34495e';ctx.strokeStyle='#2c3e50';ctx.lineWidth=2;if(type.includes('003')||type.includes('Amphibious')){ctx.beginPath();ctx.moveTo(x-40,y+10);ctx.lineTo(x-45,y);ctx.lineTo(x-45,y-8);ctx.lineTo(x+45,y-8);ctx.lineTo(x+45,y);ctx.lineTo(x+40,y+10);ctx.closePath();ctx.fill();ctx.stroke();ctx.fillStyle='#95a5a6';ctx.fillRect(x-45,y-8,90,4);ctx.fillStyle='#2c3e50';ctx.fillRect(x+10,y-20,18,12);ctx.strokeStyle='#7f8c8d';ctx.lineWidth=3;ctx.beginPath();ctx.moveTo(x+19,y-20);ctx.lineTo(x+19,y-28);ctx.stroke();ctx.fillStyle='#7f8c8d';ctx.fillRect(x+16,y-30,6,2);}else{ctx.beginPath();ctx.moveTo(x-35,y+10);ctx.lineTo(x-38,y);ctx.lineTo(x-38,y-6);ctx.lineTo(x+38,y-6);ctx.lineTo(x+38,y);ctx.lineTo(x+35,y+10);ctx.closePath();ctx.fill();ctx.stroke();ctx.fillStyle='#2c3e50';ctx.fillRect(x-15,y-16,22,10);ctx.fillRect(x+15,y-16,10,10);ctx.fillStyle='#3498db';for(let i=0;i<4;i++){ctx.fillRect(x-12+i*5,y-14,3,4);}ctx.strokeStyle='#7f8c8d';ctx.lineWidth=2;ctx.beginPath();ctx.moveTo(x+20,y-16);ctx.lineTo(x+20,y-24);ctx.stroke();ctx.fillStyle='#95a5a6';ctx.fillRect(x-25,y+2,20,8);}ctx.strokeStyle='rgba(255,255,255,0.5)';ctx.lineWidth=2;ctx.beginPath();ctx.arc(x-45,y,8,-Math.PI/3,Math.PI/3);ctx.stroke();ctx.restore();ctx.fillStyle='white';ctx.font='bold 11px Arial';ctx.textAlign='center';ctx.fillText(type,x,y+30);}
        function drawCity(x,y,name){ctx.fillStyle='#2c3e50';ctx.beginPath();ctx.arc(x,y,4,0,Math.PI*2);ctx.fill();ctx.fillStyle='#000';ctx.font='13px Arial';ctx.textAlign='left';ctx.fillText(name,x+8,y+4);}
        function drawTextBox(x,y,width,title,text,number){ctx.shadowColor='rgba(0,0,0,0.3)';ctx.shadowBlur=10;ctx.shadowOffsetX=2;ctx.shadowOffsetY=2;ctx.fillStyle='rgba(255,255,255,0.95)';ctx.strokeStyle='#2c3e50';ctx.lineWidth=2;ctx.fillRect(x,y,width,100);ctx.strokeRect(x,y,width,100);ctx.shadowColor='transparent';ctx.shadowBlur=0;ctx.shadowOffsetX=0;ctx.shadowOffsetY=0;ctx.fillStyle='#2c3e50';ctx.font='bold 16px Arial';ctx.textAlign='left';ctx.fillText(number+'.',x+10,y+25);ctx.font='bold 14px Arial';const words=title.split(' ');let line='';let yPos=y+25;for(let word of words){const testLine=line+word+' ';const metrics=ctx.measureText(testLine);if(metrics.width>width-50){ctx.fillText(line,x+35,yPos);line=word+' ';yPos+=18;}else{line=testLine;}}ctx.fillText(line,x+35,yPos);ctx.font='12px Arial';const descWords=text.split(' ');line='';yPos+=20;for(let word of descWords){const testLine=line+word+' ';const metrics=ctx.measureText(testLine);if(metrics.width>width-25){ctx.fillText(line,x+12,yPos);line=word+' ';yPos+=16;}else{line=testLine;}}ctx.fillText(line,x+12,yPos);}
        function drawArrow(x1,y1,x2,y2,color='#c0392b',dashed=false,width=3){ctx.save();ctx.strokeStyle=color;ctx.fillStyle=color;ctx.lineWidth=width;if(dashed){ctx.setLineDash([8,5]);}ctx.beginPath();ctx.moveTo(x1,y1);ctx.lineTo(x2,y2);ctx.stroke();const angle=Math.atan2(y2-y1,x2-x1);ctx.beginPath();ctx.moveTo(x2,y2);ctx.lineTo(x2-12*Math.cos(angle-Math.PI/6),y2-12*Math.sin(angle-Math.PI/6));ctx.lineTo(x2-12*Math.cos(angle+Math.PI/6),y2-12*Math.sin(angle+Math.PI/6));ctx.closePath();ctx.fill();ctx.restore();}
        function drawDiamond(x,y,size,label,crossed=false){ctx.fillStyle='#c0392b';ctx.strokeStyle='#fff';ctx.lineWidth=2;ctx.beginPath();ctx.moveTo(x,y-size);ctx.lineTo(x+size,y);ctx.lineTo(x,y+size);ctx.lineTo(x-size,y);ctx.closePath();ctx.fill();ctx.stroke();if(crossed){ctx.strokeStyle='#fff';ctx.lineWidth=3;ctx.beginPath();ctx.moveTo(x-size/2,y-size/2);ctx.lineTo(x+size/2,y+size/2);ctx.moveTo(x+size/2,y-size/2);ctx.lineTo(x-size/2,y+size/2);ctx.stroke();}ctx.fillStyle='white';ctx.font='bold 11px Arial';ctx.textAlign='center';ctx.fillText(label,x,y+4);}
        function drawSOF(x,y){ctx.fillStyle='#f39c12';ctx.strokeStyle='#fff';ctx.lineWidth=2;ctx.beginPath();ctx.moveTo(x,y-14);ctx.lineTo(x+14,y);ctx.lineTo(x,y+14);ctx.lineTo(x-14,y);ctx.closePath();ctx.fill();ctx.stroke();ctx.fillStyle='#000';ctx.font='bold 10px Arial';ctx.textAlign='center';ctx.fillText('SOF',x,y+4);}
        function drawHelicopter(x,y){ctx.fillStyle='#2c3e50';ctx.fillRect(x-10,y-5,20,10);ctx.strokeStyle='#2c3e50';ctx.lineWidth=3;ctx.beginPath();ctx.moveTo(x-15,y);ctx.lineTo(x+15,y);ctx.stroke();ctx.beginPath();ctx.moveTo(x,y-8);ctx.lineTo(x,y-12);ctx.stroke();}
        function drawAircraft(x,y,angle=0){ctx.save();ctx.translate(x,y);ctx.rotate(angle);ctx.fillStyle='#2c3e50';ctx.beginPath();ctx.moveTo(12,0);ctx.lineTo(-8,-6);ctx.lineTo(-8,6);ctx.closePath();ctx.fill();ctx.restore();}
        function drawParatrooper(x,y){ctx.fillStyle='#2c3e50';ctx.beginPath();ctx.arc(x,y,3,0,Math.PI*2);ctx.fill();ctx.strokeStyle='#ecf0f1';ctx.lineWidth=1.5;ctx.beginPath();ctx.arc(x,y-8,6,0,Math.PI*2);ctx.stroke();ctx.strokeStyle='#ecf0f1';ctx.beginPath();ctx.moveTo(x,y-2);ctx.lineTo(x,y-8);ctx.stroke();}
        function drawExplosion(x,y,size=15){ctx.fillStyle='rgba(255,107,107,0.5)';ctx.beginPath();ctx.arc(x,y,size,0,Math.PI*2);ctx.fill();ctx.fillStyle='rgba(243,156,18,0.6)';ctx.beginPath();ctx.arc(x,y,size*0.6,0,Math.PI*2);ctx.fill();}
        function drawBase(){ctx.clearRect(0,0,canvas.width,canvas.height);ctx.fillStyle='#a8d5e2';ctx.fillRect(0,0,canvas.width,canvas.height);outlyingIslands.forEach(isle=>drawIsland(isle,'#d9cbb3','#a89a7b'));drawIsland(island.points);cities.forEach(city=>drawCity(city.x,city.y,city.name));ships.forEach(ship=>drawShip(ship.x,ship.y,ship.type));}
        function drawPhase(phase){drawBase();for(let i=0;i<=phase;i++){switch(i){case 0:cities.forEach(city=>{ctx.strokeStyle='#c0392b';ctx.lineWidth=3;ctx.beginPath();ctx.arc(city.x,city.y,25,0,Math.PI*2);ctx.stroke();ctx.strokeStyle='#c0392b';ctx.lineWidth=2;ctx.beginPath();ctx.moveTo(city.x-20,city.y);ctx.lineTo(city.x+20,city.y);ctx.moveTo(city.x,city.y-20);ctx.lineTo(city.x,city.y+20);ctx.stroke();});break;case 1:drawArrow(500,200,505,340,'#c0392b',true);drawArrow(550,200,605,390,'#c0392b',true);drawDiamond(505,340,12,'XX');drawDiamond(605,390,12,'XX');break;case 2:drawSOF(450,760);drawSOF(650,840);drawHelicopter(230,700);drawHelicopter(230,750);drawAircraft(900,640,-0.8);drawAircraft(920,660,-0.8);drawArrow(900,640,820,710,'#c0392b',true,2);break;case 3:const breachPoints=[{x:680,y:520},{x:720,y:500},{x:760,y:510}];breachPoints.forEach(point=>{drawDiamond(point.x,point.y,10,'XX',true);drawExplosion(point.x,point.y,18);drawArrow(300,520,point.x-15,point.y,'#c0392b',true,2);});drawExplosion(250,740,20);drawDiamond(250,740,12,'XX');break;case 4:drawArrow(480,360,680,520,'#c0392b',false);drawArrow(520,340,720,500,'#c0392b',false);drawArrow(560,330,760,510,'#c0392b',false);drawDiamond(680,520,12,'XX');drawDiamond(720,500,12,'XX');drawDiamond(760,510,12,'XX');break;case 5:drawArrow(680,520,750,610,'#c0392b',false);drawArrow(720,500,820,590,'#c0392b',false);drawArrow(1000,730,920,700,'#c0392b',false);drawArrow(1000,760,920,730,'#c0392b',false);drawDiamond(920,700,12,'XX',true);drawDiamond(920,730,12,'XX',true);drawParatrooper(880,650);drawParatrooper(900,640);break;case 6:drawArrow(750,610,820,710,'#c0392b',false);drawArrow(820,590,920,590,'#c0392b',false);drawDiamond(820,710,12,'XX');drawDiamond(920,590,12,'XX');drawDiamond(850,800,12,'XX');drawHelicopter(300,650);drawHelicopter(320,680);drawExplosion(820,710,20);drawExplosion(920,590,20);break;}}for(let i=0;i<=phase;i++){switch(i){case 0:drawTextBox(930,30,250,'Joint targeting will support information dominance','degrade military headquarters, long-range artillery, air defense, coastal defenses, and vital infrastructure such as communications and power generation facilities.',1);break;case 1:drawTextBox(380,30,250,'Preliminary operations will capture outlying islands','to secure sea and air routes leading to the primary target.',2);break;case 2:drawTextBox(15,810,355,'Airborne and air assault groups','will conduct reconnaissance and destroy capabilities and targets such as communications, airfields, radars, C2, and supply depots.',3);break;case 3:drawTextBox(15,510,245,'The barrier breaching and clearing detachment','will remove obstacles and will open lanes for attacking ground forces.',4);break;case 4:drawTextBox(520,430,300,'The assault landing groups will conduct simultaneous landings','on multiple beaches using tactical amphibious vehicles.',5);break;case 5:drawTextBox(855,520,290,'Landing forces will quickly break through defenses','to defeat the enemy in depth and destroy air defense networks and long-range fires.',6);break;case 6:drawTextBox(45,85,290,'The deep attack group follows','using civilian roll-on/roll-off ships and landing barges to attack the enemy in depth.',7);break;}}}
        nextBtn.addEventListener('click',()=>{if(currentPhase<6){currentPhase++;drawPhase(currentPhase);if(currentPhase===6){nextBtn.textContent='Complete';nextBtn.disabled=true;}}});
        resetBtn.addEventListener('click',()=>{currentPhase=0;drawPhase(currentPhase);nextBtn.textContent='Next Phase';nextBtn.disabled=false;});
        drawPhase(0);
    </script>
</body>
</html>
{{< /rawhtml >}}
