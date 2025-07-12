---
title: 创建单位对象数据
sections:
- 简介
- UnitDefinition
- HeroDefinition
- BuildingDefinition
---

### 简介

在魔兽争霸III中，基本上所有单位都具有相同的属性，但某些选项仅在单位被标记为英雄或建筑时才可用。
因此，标准库定义被分为 `UnitDefinition`、`HeroDefinition`、`BuildingDefinition` 和 `BuildingAndHeroDefinition`。

### UnitDefinition

`UnitDefinition` 包含了所有既不是英雄也不是建筑的单位。为避免处理原始ID和ID冲突，您应该使用 `IdGenerator` 包中提供的预设ID生成器。

> 请注意，魔兽争霸III会将在ID以大写字母开头的任何单位都视作英雄，即使未使用 HeroDefinition。

来自 `DummyRecycler` 的示例：

```wurst
let DUMMY_UNIT_ID = compiletime(UNIT_ID_GEN.next())

@compiletime function gen()
	new UnitDefinition(DUMMY_UNIT_ID, UnitIds.wisp)
		..setName("Effect Dummy Unit")
		..setUpgradesUsed("")
		..setStructuresBuilt("")
		..setManaRegeneration(0.1)
		..setManaInitialAmount(1000)
		..setManaMaximum(1000)
		..setCollisionSize(0.0)
		..setRace(Race.Commoner)
		..setFoodCost(0)
		..setArmorType(ArmorType.Divine)
		..setIconGameInterface(Icons.bTNtemp)
		..setSpeedBase(522)
		..setModelFile("dummy.mdl")
		..setAnimationBlendTimeseconds(0.0)
		..setAnimationCastBackswing(0.0)
		..setMaximumPitchAngledegrees(0.0)
		..setMaximumRollAngledegrees(0.0)
		..setArtSpecial("")
		..setProjectileImpactZ(0.0)
		..setProjectileLaunchZ(0.0)
		..setNameEditorSuffix("(Basics)")
		..setSightRadiusDay(1)
		..setSightRadiusNight(1)
		..setUnitClassification("_")
		..setPropulsionWindowdegrees(1.0)
		..setTooltipBasic("")
		..setNormalAbilities(commaList(AbilityIds.invulnerable, AbilityIds.locust))
```

### HeroDefinition

对于非建筑的英雄，请使用 `HeroDefinition`。除了使用 `HeroDefinition` 之外，您还需要提供一个以大写字母开头的ID。请使用 `HERO_ID_GEN` ID生成器。基于圣骑士的英雄示例：

> 如果提供的ID不是以大写字母开头，该单位将不会成为英雄。

```wurst
let ESC_STRONG_ID = compiletime(HERO_ID_GEN.next())

@compiletime function gen()
	new HeroDefinition(ESC_STRONG_ID, UnitIds.paladin)
		..setIconGameInterface(Icons.bTNFootman)
		..setIconScoreScreen("")
		..setModelFile(Units.theCaptain1)
		..setCollisionSize(0)
		..setNormalAbilities(commaList(GHOST_VIS_ID, AbilityIds.inventory))
		..setHotkey("")
		..setProperNames(commaList("Mike", "Jason", "Rocky", "Herold", "Arthur", "Chev"))
		..setProperNamesUsed(6)
		..setName("|cff084294Strong Escaper")
		..setManaInitialAmount(0)
		..setStartingAgility(0)
		..setStartingIntelligence(0)
		..setStartingStrength(0)
		..setHitPointsMaximumBase(300)
		..setAgilityPerLevel(0)
		..setStrengthPerLevel(0)
		..setIntelligencePerLevel(0)
		..hideHeroDeathMsg(true)
```

### BuildingDefinition

分别使用 `BuildingDefinition` 或 `BuildingAndHeroDefinition` 来创建建筑。

```wurst
let WAYPOINT_ID = compiletime(UNIT_ID_GEN.next())

@compiletime function gen()
	new BuildingDefinition(WAYPOINT_ID, UnitIds.circleofpower)
	..setName("|cffFF7B29Waypoint")
	..setHotkey("R")
	..setTooltipBasic("|cffFFCC00Build |cffFF7B29Waypoint [R]")
	..setTooltipExtended("Orders Escaperkillers to move to the Rallypoint." +
	"\nUsefull if you want your units not only to go a straight line.")
	..setIconGameInterface("ReplaceableTextures\\CommandButtons\\BTNWaypoint.blp")
	..setNormalAbilities(commaList(TURN_OFF_ID, INFO_WAYPOINT_ID, AND_CONNECTION_ID, DECREASE_DELAY_ID, INCREASE_DELAY_ID, NO_WISPS_ID,
								  REMOVE_OBJECT_ID, RALLY_POINT, GHOST_INVIS_ID, INVULNERABILITY_ID, CLEAR_CONNECTION_ID))
	..setButtonPositionX(3)
	..setButtonPositionY(0)
	..setTintingColorGreen(128)
	..setTintingColorBlue(0)
	..setSightRadiusDay(0)
	..setSightRadiusNight(0)
	..setBuildTime(1)
	..setCollisionSize(0)
	..setScalingValue(0.8)
	..setSelectionScale(2.2)
	..setPathingMap("none")
	..setHideMinimapDisplay(true)
```