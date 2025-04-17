Update the getDoppelgangerImages function:
vp_game-main
const getDoppelgangerImages = (directory, value, chosenPlayerSrc) => {
  if (!chosenPlayerSrc) {
    console.error("chosenPlayerSrc is undefined");
    return [];
  }
  let chosenInfo = chosenPlayerSrc;
  let chosenId = chosenInfo.substring(chosenInfo.length - 14, chosenInfo.length - 12);
  if (chosenId.substring(0, 1) === "/") {
    chosenId = chosenId.substring(1, 2);
  }

  let gender = parseInt(chosenId) < 12 ? '0' : '1';
  let genderOffset = gender === '0' ? 0 : 12;

  const imagesToReturn = [];

  const race = chosenInfo.substring(chosenInfo.length - 9, chosenInfo.length - 8);
  const background = chosenInfo.substring(chosenInfo.length - 7, chosenInfo.length - 6);
  const costume = chosenInfo.substring(chosenInfo.length - 5, chosenInfo.length - 4);

  const idsToSkip = [chosenId];
  if (gender === '0') idsToSkip.push('4');

  for (let i = genderOffset; i < (12 + genderOffset); i++) {
    if (idsToSkip.includes(i.toString())) continue;

    let file;
    if (value <= 1) {
      file = `${i}_${gender}_${race}_${background}_${costume}`;
    } else if (value === 2) {
      file = `${i}_${gender}_${race}_${getRandomInt(2)}_${getRandomInt(9)}`;
    } else if (value === 3) {
      file = `${i}_${gender}_${race}_${background}_${getRandomInt(9)}`;
    } else {
      file = `${i}_${gender}_${race}_${background}_${costume}`;
    }

    imagesToReturn.push(`/images/doppelganger/${file}.png`);
  }

  imagesToReturn.push(`/images/doppelganger/${chosenId}_${gender}_${race}_${background}_${costume}.png`);

  return shuffle(imagesToReturn);
};
Apply
2.
We updated the JSX where you render the doppelganger images:
{currentPlayer && !showPasswordPage && (
  <div className="doppelganger-section">
    <input
      type="range"
      min="0"
      max="4"
      value={sliderValue}
      onChange={handleSliderChange}
      className="slider"
    />
    <div className="doppelganger-images">
      {doppelgangerImages.map((image, index) => (
        <img 
          key={index} 
          src={image} 
          alt={`Doppelganger ${index + 1}`} 
          onError={(e) => {
            console.error(`Failed to load image: ${image}`);
            e.target.src = '/images/placeholder.png'; // Replace with a placeholder image
          }}
        />
      ))}
    </div>
  </div>
)}
3.
Make sure your images are actually in the correct folder: /public/images/doppelganger/.
4.
Add some console logging to help debug:
useEffect(() => {
  if (currentPlayer) {
    const initialImages = getDoppelgangerImages(imageDir, sliderValue, currentPlayer.src);
    console.log("Doppelganger images:", initialImages);
    setDoppelgangerImages(initialImages);
  }
}, [sliderValue, currentPlayer]);
