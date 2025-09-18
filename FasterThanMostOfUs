
s.boot;






(
(
b = Buffer.read(s, Platform.resourceDir +/+ "sounds/a11wlk01.wav");
)


x = Synth(\granulator, [\bufnum, b, \density, 30, \dur, 0.1, \amp, 0.4]);
x.free;

SynthDef(\granulator, {
    arg bufnum, rate = 1, startPos = 0, dur = 0.2, amp = 1, density = 20, pan = 0;
    var grains, trig, pos;


    trig = Impulse.kr(density);


    pos = Phasor.kr(
        trig: 0,
        rate: rate * BufRateScale.kr(bufnum) / SampleRate.ir,
        start: 0,
        end: 1,
        resetPos: startPos
    );


    grains = GrainBuf.ar(
        2,
        trig,
        dur,
        bufnum,
        rate,
        pos,
        2,
        pan
    );

    Out.ar(0, grains * amp);
}).add;



SynthDef(\bass, { |out, freq = 440, gate = 1, amp = 0.2, slideTime = 0.17, ffreq = 1100, width = 0.15,
        detune = 1.005, preamp = 4|
    var sig, env;
    env = Env.adsr(0.01, 0.3, 0.4, 0.1);
    freq = Lag.kr(freq, slideTime);
    sig = Mix(VarSaw.ar([freq, freq * detune], 0, width, preamp)).distort * amp
        * EnvGen.kr(env, gate, doneAction: Done.freeSelf);
    sig = LPF.ar(sig, ffreq);
    Out.ar(out, sig ! 2)
}).add;






SynthDef(\bassSynth, {
	|freq = 440, amp = 0.5, gate = 1|
	var env = Env.adsr(0.01,0.2, 0.4,0.5).kr(gate: gate);
	var osc = Saw.ar(freq) * env;
	var sound = LPF.ar(osc, 1000) *amp;
	Out.ar(0,Pan2.ar(sound));
}).add;




SynthDef(\chipLead, { |freq=440, amp=0.2, out=0|
	var env = Env.perc(0.01, 0.3).kr(doneAction:2);
	var sig = Pulse.ar(freq*[1,1.01], 0.4).mean * env * amp;
	Out.ar(out, sig!2);
}).add;


SynthDef(\chipSnare, { |amp=0.3, out=0|
	var env = Env.perc(0.001, 0.15).kr(doneAction:2);
	var sig = WhiteNoise.ar * env * amp;
	Out.ar(out, sig!2);
}).add;


SynthDef(\chipKick, { |amp=0.6, out=0|
	var env = Env.perc(0.001, 0.25).kr(doneAction:2);
	var sig = SinOsc.ar(Env([80,30],[0.15]).kr) * env * amp;
	Out.ar(out, sig!2);
}).add;

SynthDef(\pad, { |freq=440, amp=0.2, out=0|
	var env = Env.linen(0.1, 2, 2).kr(doneAction:2);
	var sig = Saw.ar(freq * [1,1.01]);
	sig = LPF.ar(sig, 1200);
	sig = FreeVerb.ar(sig, mix:0.8, room:0.98, damp:0.4);
	Out.ar(out, sig * env * amp);
}).add;

Synth(\pad);

x = Synth(\bassSynth);

Synth(\chipSnare);
Synth(\bass);
)




//~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

(
t = TempoClock(150/60).permanent_(true); // arcade tempo

~chords = [
			Pseq([0,5,5,8], 8),   // chord 6
			Pseq([15,12,8,7], 8),   // chord 1
			Pseq([3,7,7,10], 4),   // chord 5
			Pseq([2,2,7,10], 4), // chord 5 w/ D for bass
			Pseq([-2,2,2,5], 8)   // chord 3maj
		];

// Kick
Pdef(\kick,
	Pbind(
		\instrument, \chipKick,
		\dur, 0.5,
		\amp, 0.6
	)
).play(t);



// Snare
Pbindef(\snare,
	Pbind(
		\instrument, \chipSnare,
		\dur, 2,
		\amp, 0.1,
		\legato, 1
	)
).play(t);


Pdef(\pad,
	Pbind(
		\instrument, \pad,
		\scale, Scale.minor,
		\note, Pseq([
			[0, 5, 8],   // chord 6
			[8, 12, 15],  // chord 1
			[3, 7, 10],   // chord 5
			[2, 5, 10]     // chord 3
		], inf),
		\dur, 8,
		\amp, 0.2
	)
).play(t);


Pdef(\bass,
	Pbind(
		\instrument, \bass,
		\note, Pseq(~chords, inf),
		\octave, 4,
		\dur, 0.25,
		\amp, 0.45
	)
).play(t);


Pdef(\bass2,
	Pbind(
		\instrument, \bassSynth,
		\scale, Scale.minor,
		\note, Pseq([
			Pseq([-7,-7,0,0], 8),   // chord 6
			Pseq([15,12,8,7], 8),   // chord 1
			Pseq([3,3,7,10], 4),   //chord 5
			Pseq([2,2,7,10], 4), // chord 5 w/ D for bass
			Pseq([-2,2,2,5], 8)   // chord 3maj
		], inf),
		\octave, 3,
		\dur, 0.25,
		\amp, 0.45
	)
).play(t);




Pdef(\chords,
	Pbind(
		\instrument, \bass,
		\scale, Scale.minorPentatonic,
		\note, Pseq([
			[5, 8, 12],   // 6
			[8, 12, 15],   // 1
			[3, 7, 10],   // 5
			[2, 5, 10]    // 3
		], inf),
		\dur, Pseq([8, 8, 8, 8], inf),
		\amp, 0.4,
		\legato, 1
	)
).play(t);




Pdef(\lead,
	Pbind(
		\instrument, \chipLead,
		\scale, Scale.minorPentatonic,
		\degree, Prand([0,2,4,7,9], inf),
		\octave, Pwhite(3,5, inf),
		\dur, 0.25,
		\amp, 0.05
	)
).play(t);

Ndef(\gran, {
	var trig = Dust.kr(5);
	var pos  = LFNoise1.kr(0.05).range(0,1);
	var sig  = GrainBuf.ar(2, trig, 0.2, b, 1, pos, 2, LFNoise1.kr(0.2));
	sig = FreeVerb.ar(sig, 0.8, 0.9, 0.7);
	sig * 0.8
}).play;

Pdef(\leadEcho,
	Pbind(
		\instrument, \chipLead,
		\scale, Scale.minorPentatonic,
		\degree, Prand([0,2,4,7,9], inf),
		\octave, 4,
		\dur, Prand ([1,0.5,0.25],inf),
		\amp, 0.1,
		\legato, 2
	)
).play(t);
)


//---------------------------------------------- 2nd -----------------------------------------------------
t = TempoClock(150/60).permanent_(true); // arcade tempo

Ndef(\gran).play;

Pdef(\bass2).play(t);
Pdef(\bass).play(t);

Pdef(\leadEcho).play(t);
Pdef(\lead).play(t);

Pdef(\chords).play(t);
Pdef(\pad).play(t);

Pbindef(\snare).play(t);
Pdef(\kick).play(t);


Ndef(\gran2nd).stop;

Pdef(\lead2nd).stop;

Pdef(\bass22nd).stop;
Pdef(\bass2nd).stop;

Pdef(\chords2nd).stop;
Pdef(\pad2nd).stop;




        Pbindef(\snare,\dur, 0.25);
        Pbindef(\snare,\amp,0.1);
        Pbindef(\snare).stop;


Ndef(\gran).stop;

Pdef(\leadEcho).stop;
Pdef(\lead).stop;

Pdef(\bass2).stop;
Pdef(\bass).stop;

Pdef(\chords).stop;
Pdef(\pad).stop;

Pbindef(\snare).stop;
Pdef(\kick).stop;




(
~chords2 = [
			Pseq([1,5,5,8], 8),   // chord 4
			Pseq([3,7,7,10], 8),   // chord 5
			Pseq([8,12,12,15], 8),   // chord 1
			Pseq([8,5,5,1], 8)   // chord 4
		];


Pdef(\pad2nd,
	Pbind(
		\instrument, \pad,
		\scale, Scale.minor,
		\note, Pseq([
			[1, 5, 8],   // chord 4
			[3, 7, 10],  // chord 5
			[3, 8, 12],   // chord 1
			[1, 5, 8]     // chord 4
		], inf),
		\dur, 8,
		\amp, 0.2
	)
).play(t);



Pdef(\bass22nd,
	Pbind(
		\instrument, \bassSynth,
		\scale, Scale.minor,
		\note, Pseq([
			Pseq([-4,-4,1,1], 8),   // chord 4
			Pseq([-2,-2,3,3], 8),   // chord 5
			Pseq([0,3,5,8], 8),   //chord 1
			Pseq([5,1,0,-4], 8)   // chord 4
		], inf),
		\octave, 3,
		\dur, 0.25,
		\amp, 0.45
	)
).play(t);

// Bassline
Pdef(\bass2nd,
	Pbind(
		\instrument, \bass,
		\note, Pseq(~chords2, inf),
		\octave, 4,
		\dur, 0.25,
		\amp, 0.45
	)
).play(t);




~chordsInst = Pdef(\chords2nd,
	Pbind(
		\instrument, \bass,
		\scale, Scale.minorPentatonic,
		\note, Pseq([
			[1, 5, 8],   // 4
			[3, 7, 12],   // 5
			[3, 8, 12],   // 1 w/ 12 instead of 10
			[1, 5, 12]    // 4 w/ maj7
		], inf),
		\dur, Pseq([8, 8, 8, 8], inf),
		\amp, 0.4,
		\legato, 1
	)
).play(t);

Pdef(\lead2nd,
	Pbind(
		\instrument, \chipLead,
		\scale, Scale.minorPentatonic,
		\note, Prand([1,3,5,7,8,10], inf),
		\octave, Pwhite(3,5, inf),
		\dur, 0.25,
		\amp, 0.05
	)
).play(t);

Ndef(\gran2nd, {
	var trig = Dust.kr(5);
	var pos  = LFNoise1.kr(0.05).range(0,1);
	var sig  = GrainBuf.ar(2, trig, 0.2, b, 1, pos, 2, LFNoise1.kr(0.2));
	sig = FreeVerb.ar(sig, 0.8, 0.9, 0.7);
	sig * 0.8
}).play;
)



